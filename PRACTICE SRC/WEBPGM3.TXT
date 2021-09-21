     H DFTACTGRP(*NO) ACTGRP(*NEW) BNDDIR('ANKITWEB1/TESTB1')
      *---------Procedure declaration for GET command on web-----------*
     D*Getenv           pr              *   extproc('QtmhGetEnv')
     D* var                             *   value Options(*string)
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
      *--------------Main Program---------------*
      /Free
         // setting up header
         DATA = 'Content-type: Text/html' + CRLF + CRLF ;
         writetoweb(DATA: %len(%trim(DATA)): ErrDs);
         DATA = 'This data will written on web'+ CRLF;
         writetoweb(DATA: %len(%trim(DATA)): ErrDs);

         DATA = 'Hey Ankit, Welcome to the web services'+ CRLF;
         writetoweb(DATA: %len(%trim(DATA)): ErrDs);

         *Inlr = *On;
         Return;
      /End-Free
