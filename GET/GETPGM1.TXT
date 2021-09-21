     h
      *--------------   File Declaration-------------------------------*
     FWEBPF1    IF A E           K DISK

      *--------------   Variables    ----------------------------------*
     d MSG             S            100A   VARYING
     d HEAD            S            100A   VARYING
     D URL             S            100A   VARYING
     D HEADER          S            100A   VARYING
     D BODY            S            100A   VARYING
     D IDC             S             10A   VARYING
     D ID              S              5S 0 INZ

      *--------------   Main program  ---------------------------------*
       /free
          exec sql
            set option commit = *none;
            
            Exec sql CALL QSYS2.QCMDEXC('CHGJOB CCSID(37)');

          DSPLY 'ENTER USER ID:' '' IDC;

          URL = 'https://gorest.co.in/public/v1/posts/'+%TRIM(IDC);
          HEADER ='<httpheader></httpheader>';

      *--------------   GET URL Validation ----------------------*
          MONITOR;
            exec sql
              select coalesce(varChar(responseMsg,20000),' '),
              varChar(responseHttpHeader,10000) INTO
              :S1,:HEAD
              from Table(Systools.HttpGetClobVerbose(
              Trim(:URL),
              Trim(:HEADER)));
            WRITE SREC;
            DSPLY 'GET SUCCESSFULLY';
          ON-ERROR;
            DSPLY 'SQL ERROR';
          ENDMON;

         *INLR = *ON;
       /end-free
