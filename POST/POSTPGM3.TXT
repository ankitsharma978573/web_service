     h
     FWEBPF1    IF A E           K DISK
     d MSG             S            100A   VARYING
     d HEAD            S            100A   VARYING
     D URL             S            100A   VARYING
     d HEADER          S           1000A   VARYING
     d AUTH            S           1000A   VARYING
     d BODY            S           1000A   VARYING
       /free
          exec sql
            set option commit = *none;
            
            Exec sql CALL QSYS2.QCMDEXC('CHGJOB CCSID(37)');

          URL = 'https://gorest.co.in/public/v1/users';
          AUTH = 'f392ec33532dd36c769a3455be52151d8dcbebc'+
                 '2d2545244645c52818bdb6207';
       HEADER ='<httpHeader><header name="Content-Type" value="text/plain">'+
                  '</header><header name="Authorization" '+
                  'value="Bearer '+%trim(auth)+
                   '"></header></httpHeader>';

       body = '{'+
                   '"name":"Bhuvanesh",'+
                   '"email":"msgr_gowdasvane@bailey-barton.co",'+
                   '"gender":"Female",'+
                   '"status":"Active"'+
               '}';

          EXEC SQL
            select coalesce(varChar(responseMsg,20000),' '),
            varChar(responseHttpHeader,10000) INTO :MSG,:HEAD
            from Table(Systools.HttpPostClobVerbose(
            Trim(:URL),
            Trim(:HEADER),
            Trim(:body)));

            S1 = MSG;
            IF SQLCODE = 0;
              WRITE SREC;
              DSPLY 'ADDED SUCCESSFULLY';
            ENDIF;

         *INLR = *ON;
       /end-free
