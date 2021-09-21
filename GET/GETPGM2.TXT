     H DFTACTGRP(*NO) ACTGRP(*NEW) BNDDIR('ANKITWEB1/TESTB1')
     FEMPPF123  IF   E           K DISK
      *---------Procedure declaration for GET command on web-----------*
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
      *
     D CRLF            C                   x'0d25'
     D DATA            S           5000A
     D URL             S            100A
     D POS             S              6s 0
     D URLID           S              4S 0

      *--------------Main Program---------------*
      /Free
         // setting up header
         DATA = 'Content-type: Text/html' + CRLF + CRLF ;
         writetoweb(DATA: %len(%trim(DATA)): ErrDs);

         URL = %Str(GetEnv('REQUEST_URI'));
         POS = %SCAN('/':URL);
         POS = %SCAN('/':URL:POS+1) + 1;
         POS = %SCAN('/':URL:POS+1) + 1;

         MONITOR;
           URLID = %DEC(%SUBST(URL:POS:%LEN(%TRIM(URL))-(POS-1)):4:0);
           CHAIN URLID EMPPF123;
           IF %FOUND(EMPPF123);
             DATA = 'EMPLOYEE ID:'+(%CHAR(EID))+CRLF +
                    'EMPLOYEE NAME:'+ENAM+CRLF +
                    'EMPLOYEE AGE:'+(%CHAR(EAGE))+CRLF +
                    'EMPLOYEE SALARY:'+(%CHAR(ESALARY))+CRLF;
           ELSE;
             DATA = 'INVALID USER ID';
           ENDIF;
         ON-ERROR;
           DATA = 'INVALID URL';
         ENDMON;
         writetoweb(DATA: %len(%trim(DATA)): ErrDs);

         *Inlr = *On;
         Return;
      /End-Free
