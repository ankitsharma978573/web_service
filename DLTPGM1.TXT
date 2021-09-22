      *----------------- DELETE CONSUMING ------------------------*
     h
      *----------------- FILE DECLARATION ------------------------*
     FWEBPF1    IF A E           K DISK
      *----------------- VARIABLES   ------------------------*
     d MSG             S            100A   VARYING
     d HEAD            S            100A   VARYING
     D URL             S            100A   VARYING
     d HEADER          S           1000A   VARYING
     d AUTH            S           1000A   VARYING
     d BODY            S           1000A   VARYING
     d IDC             S              6A   VARYING

      *------------------MAIN PROGRAM------------------------*
       /free
          exec sql
            set option commit = *none;

            Exec sql CALL QSYS2.QCMDEXC('CHGJOB CCSID(37)');

            DSPLY 'ENTER ID WANT TO DELETE:' '' IDC;

          URL = 'https://gorest.co.in/public/v1/todos/,'+%TRIM(IDC);
          AUTH = 'f392ec33532dd36c769a3455be52151d8dcbebc'+
                 '2d2545244645c52818bdb6207';
          HEADER ='<httpHeader><header name="Content-Type" value="text/plain">'+
                  '</header><header name="Authorization" '+
                  'value="Bearer '+%trim(auth)+
                   '"></header></httpHeader>';

          EXEC SQL
            select coalesce(varChar(responseMsg,20000),' '),
            varChar(responseHttpHeader,10000) INTO :MSG,:HEAD
            from Table(Systools.HTTPDELETECLOBVERBOSE(
            Trim(:URL),
            Trim(:HEADER)));

            IF SQLCODE = 0;
              DSPLY 'DELETED SUCCESSFULLY';
            ELSE;
              DSPLY 'ERROR WHILE DELETING';
            ENDIF;

         *INLR = *ON;
       /end-free
