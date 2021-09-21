     H DFTACTGRP(*NO) ACTGRP(*NEW) BNDDIR('ANKITWEB1/TESTB1':'QC2LE')
     Femppf123  IF A E           K DISK
      *---------Procedure declaration for GET command on web----------*
     DGetenv           pr              *   extproc('getenv')
     D var                             *   value Options(*string)

      *---------Procedure declaration for write data on web-----------*
     Dwritetoweb       pr                  extproc('QtmhWrStout')
     D Datavar                    65535A   Options(*varsize)
     D Datavarlen                    10I 0 const
     D Errorcode                   8000A   Options(*Varsize)
      *-------Error data structure used as a parameter for errcode----*
     DErrDs            ds                  qualified
     D BytesProv                     10I 0 Inz(0)
     D BytesAvail                    10I 0 Inz(0)

       /COPY QSYSINC/QRPGLESRC,QUSEC

      *---------Procedure declaration for POST command on web---------*
     DReadStdInput     pr                  extproc('QtmhRdStin')
     D szRtnBuffer                65535A   Options(*varsize)
     D nBufLen                       10I 0 const
     D nRtnLen                       10I 0
     D QUSEC                               like(QUSEC)
      * --------------------- VARIABLE -------------------------------*
     D CRLF            C                   x'0d25'
     D DATA            S           5000A
     D URL             S            100A
     D REQMETHOD       S             20A
     D CONTENT         S            100A
      *
     D RtnBuffer       s           4096A   inz
     D RtnLen          s             10I 0
     D apiError        ds                  likeds(QUSEC) inz
     D veid            s              4S 0
     D vename          s             20A
     D veage           s              3S 0
     D vesalary        s             10S 0
     D LPOS            s              3S 0
     D POS             s              3S 0
     D FLAG            s              3S 0 INZ(0)

      *--------------Main Program---------------*
      /Free
         //---------- setting up header----------------
         DATA = 'Content-type: text/html' + CRLF + CRLF ;
         writetoweb(DATA: %len(%trim(DATA)): ErrDs);

         //---------- setting up header----------------
         URL = %Str(GetEnv('REQUEST_URI'));
         REQMETHOD = %STR(GetEnv('REQUEST_METHOD'));
         IF REQMETHOD = 'POST';
           CONTENT = %STR(GetEnv('CONTENT_TYPE'));
           ReadStdInput(RtnBuffer:%size(RtnBuffer):RtnLen:apiError);
         ENDIF;

         //---------- CLASSIFICATION INTO FILEDS ---------
         DATA = %trim(RtnBuffer);
         MONITOR;
           LPOS = %scan(',':DATA);
           veid = %dec(%subst(DATA:1:LPOS-1):4:0);
           POS = LPOS+1;

           LPOS = %scan(',':DATA:POS);
           vename = %subst(DATA:pos:lpos-pos);
           POS = LPOS+1;

           LPOS = %scan(',':DATA:POS);
           veage = %dec(%subst(DATA:pos:lpos-pos):3:0);
           POS = LPOS+1;

           vesalary = %dec(%subst(DATA:pos:%len(%trim(data))-(pos-1)):10:0);
           FLAG = 0;
         ON-ERROR;
           DATA = 'FORMAT INCORRECT : ID,NAME,AGE,SALARY';
           FLAG = 1;
         ENDMON;

         //---------- ADDING RECORD INTO PF-------------
         IF FLAG = 0;
          MONITOR;
            EID = VEID;
            CHAIN EID EMPPF123;
            IF %FOUND(EMPPF123);
              DATA = 'ID ALREADY EXIST';
            ELSE;
              ENAM = %TRIM(VENAME);
              EAGE = VEAGE;
              ESALARY = VESALARY;
              write erec;
              DATA = 'RECORD ADDED SUCCESSFULLY';
            ENDIF;
          ON-ERROR;
            DATA = 'ERROR WHILE INSERTING RECORD IN DATABASE';
          ENDMON;
         ENDIF;

           writetoweb(DATA: %len(%trim(DATA)): ErrDs);

         *Inlr = *On;
         Return;
      /End-Free
