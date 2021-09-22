     h
     FWEBPF1    IF A E           K DISK
     d MSG             S            100A   VARYING
     d HEAD            S            100A   VARYING
     D URL             S            100A   VARYING
     d HEADER          S           1000A   VARYING
     d AUTH            S           1000A   VARYING
     d BODY            S           1000A   VARYING
     d IDC             S              6A   VARYING
     d SEL             S              2A
     D VAL             S             20A   VARYING
       /free
          exec sql
            set option commit = *none;

            Exec sql CALL QSYS2.QCMDEXC('CHGJOB CCSID(37)');

            DSPLY 'ENTER ID WANT TO CHANGE:' '' IDC;

          URL = 'https://gorest.co.in/public/v1/comments,'+%TRIM(IDC);
          AUTH = 'f392ec33532dd36c769a3455be52151d8dcbebc'+
                 '2d2545244645c52818bdb6207';
       HEADER ='<httpHeader><header name="Content-Type" value="text/plain">'+
                  '</header><header name="Authorization" '+
                  'value="Bearer '+%trim(auth)+
                   '"></header></httpHeader>';

         DSPLY 'ENTER 1,2,3 FOR CHANGE NAME,GENDER,STATUS' '' SEL;
         SELECT;
           WHEN SEL ='1';
             DSPLY 'ENTER NAME;' '' VAL;
             body = '{'+
                   '"name":%TRIM(VAL),'+
               '}';
           WHEN SEL ='2';
             DSPLY 'ENTER GENDER;' '' VAL;
             body = '{'+
                   '"GENDER":%TRIM(VAL),'+
               '}';
           WHEN SEL ='3';
             DSPLY 'ENTER STATUS;' '' VAL;
             body = '{'+
                   '"STATUS":%TRIM(VAL),'+
               '}';
           OTHER;
             DSPLY ' INVALID OPTION';
         ENDSL;

          EXEC SQL
            select coalesce(varChar(responseMsg,20000),' '),
            varChar(responseHttpHeader,10000) INTO :MSG,:HEAD
            from Table(Systools.HttpPutClobVerbose(
            Trim(:URL),
            Trim(:HEADER),
            Trim(:body)));

            IF SQLCODE = 0;
              DSPLY 'Changed Successfully';
            ENDIF;

         *INLR = *ON;
       /end-free
