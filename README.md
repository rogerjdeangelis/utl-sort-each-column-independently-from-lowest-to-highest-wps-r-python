# utl-sort-each-column-independently-from-lowest-to-highest-wps-r-python
Sort each column independently from lowest to highest wps r python
    %let pgm=utl-sort-each-column-independently-from-lowest-to-highest-wps-r-python;

       Sort each column independently from lowest to highest wps r python

       This type of problem is best solved with a matrix like in memory capable langauge like R and Python.

       I don't recommed doing this in sql unless you paralellize.

       Solutions

         1  wps r (simple solution)

         2  wps python (simple solution)
            https://stackoverflow.com/users/4909087/cs95

         3  wps sql hard coded

         4  wps r sql
         5  wps python sql

         6  wps datastep
         7  wps sort merge

         8  failed hash (my attempt)


    github
    http://tinyurl.com/32ud62k4
    https://github.com/rogerjdeangelis/utl-sort-each-column-independently-from-lowest-to-highest-wps-r-python

    https://github.com/rogerjdeangelis/utl-summarizing-a-matrix-over-rows-and-columns-simultaneously

    /*                   _
    (_)_ __  _ __  _   _| |_
    | | `_ \| `_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    */

    libname sd1 "d:/sd1";
    options validvarname=upcase;
    data sd1.provinces;
     informat a1-a8 $2.;
     input a1-a8;
    /*
     array as a1-a8;
     do over as ;
       as = put((as - 64),z2.);
     end;
    */
    cards4;
    05 23 23 18 15 03 13 13
    21 24 01 05 05 24 22 09
    01 11 12 18 25 14 18 18
    15 06 25 05 18 18 04 04
    23 12 20 24 14 12 10 26
    02 18 23 02 23 16 19 16
    09 04 04 07 06 24 13 19
    08 07 10 07 09 24 05 26
    07 06 08 14 02 02 23 24
    01 23 16 21 03 01 02 25
    06 14 23 17 07 11 23 22
    26 24 13 07 24 05 19 17
    10 20 14 06 23 22 16 09
    11 16 13 21 17 09 13 09
    12 19 13 15 08 09 16 12
    18 01 02 19 11 12 11 12
    01 11 10 04 10 05 04 03
    06 12 06 16 05 13 13 08
    23 20 10 03 13 09 24 25
    15 07 09 08 15 02 02 06
    ;;;;
    run;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* SD1.PROVINCES total obs=20                                                                                             */
    /*                                                                                                                        */
    /* Obs    A1    A2    A3    A4    A5    A6    A7    A8                                                                    */
    /*                                                                                                                        */
    /*   1    05    23    23    18    15    03    13    13                                                                    */
    /*   2    21    24    01    05    05    24    22    09                                                                    */
    /*   3    01    11    12    18    25    14    18    18                                                                    */
    /*   4    15    06    25    05    18    18    04    04                                                                    */
    /*   5    23    12    20    24    14    12    10    26                                                                    */
    /*   6    02    18    23    02    23    16    19    16                                                                    */
    /*   7    09    04    04    07    06    24    13    19                                                                    */
    /*   8    08    07    10    07    09    24    05    26                                                                    */
    /*   9    07    06    08    14    02    02    23    24                                                                    */
    /*  10    01    23    16    21    03    01    02    25                                                                    */
    /*  11    06    14    23    17    07    11    23    22                                                                    */
    /*  12    26    24    13    07    24    05    19    17                                                                    */
    /*  13    10    20    14    06    23    22    16    09                                                                    */
    /*  14    11    16    13    21    17    09    13    09                                                                    */
    /*  15    12    19    13    15    08    09    16    12                                                                    */
    /*  16    18    01    02    19    11    12    11    12                                                                    */
    /*  17    01    11    10    04    10    05    04    03                                                                    */
    /*  18    06    12    06    16    05    13    13    08                                                                    */
    /*  19    23    20    10    03    13    09    24    25                                                                    */
    /*  20    15    07    09    08    15    02    02    06                                                                    */
    /*                                                                                                                        */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*
    / | __      ___ __  ___   _ __
    | | \ \ /\ / / `_ \/ __| | `__|
    | |  \ V  V /| |_) \__ \ | |
    |_|   \_/\_/ | .__/|___/ |_|
                 |_|
    */

    proc datasets lib=sd1 nolist mt=data mt=view nodetails;delete want; run;quit;

    %utl_submit_wps64x('
    libname sd1 "d:/sd1";
    proc r;
    export data=sd1.provinces r=provinces;
    submit;
      savcol=colnames(provinces);
      want<-provinces[do.call(order, provinces),];
      want <- apply(provinces, 2, sort, decreasing=F);
      colnames(want)=savcol;
    endsubmit;
    import r=want  data=sd1.want;
    run;quit;
    ');

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* FOR SOME REASON RENAME DID NOT WORK IN R                                                                               */
    /*                                                                                                                        */
    /* SD1.WANT total obs=20                                                                                                  */
    /*                                                                                                                        */
    /* Obs    V1    V2    V3    V4    V5    V6    V7    V8                                                                    */
    /*                                                                                                                        */
    /*   1    01    01    01    02    02    01    02    03                                                                    */
    /*   2    01    04    02    03    03    02    02    04                                                                    */
    /*   3    01    06    04    04    05    02    04    06                                                                    */
    /*   4    02    06    06    05    05    03    04    08                                                                    */
    /*   5    05    07    08    05    06    05    05    09                                                                    */
    /*   6    06    07    09    06    07    05    10    09                                                                    */
    /*   7    06    11    10    07    08    09    11    09                                                                    */
    /*   8    07    11    10    07    09    09    13    12                                                                    */
    /*   9    08    12    10    07    10    09    13    12                                                                    */
    /*  10    09    12    12    08    11    11    13    13                                                                    */
    /*  11    10    14    13    14    13    12    13    16                                                                    */
    /*  12    11    16    13    15    14    12    16    17                                                                    */
    /*  13    12    18    13    16    15    13    16    18                                                                    */
    /*  14    15    19    14    17    15    14    18    19                                                                    */
    /*  15    15    20    16    18    17    16    19    22                                                                    */
    /*  16    18    20    20    18    18    18    19    24                                                                    */
    /*  17    21    23    23    19    23    22    22    25                                                                    */
    /*  18    23    23    23    21    23    24    23    25                                                                    */
    /*  19    23    24    23    21    24    24    23    26                                                                    */
    /*  20    26    24    25    24    25    24    24    26                                                                    */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*___                                     _   _
    |___ \  __      ___ __  ___   _ __  _   _| |_| |__   ___  _ __
      __) | \ \ /\ / / `_ \/ __| | `_ \| | | | __| `_ \ / _ \| `_ \
     / __/   \ V  V /| |_) \__ \ | |_) | |_| | |_| | | | (_) | | | |
    |_____|   \_/\_/ | .__/|___/ | .__/ \__, |\__|_| |_|\___/|_| |_|
                     |_|         |_|    |___/
    */

    %utl_submit_wps64x("
    options validvarname=any lrecl=32756;
    libname sd1 'd:/sd1';
    proc python;
    export data=sd1.provinces python=df;
    submit;
    import numpy as np;
    df[:] = np.sort(df.values, axis=0);
    print(df);
    endsubmit;
    ");

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* The WPS PYTHON Procedure                                                                                               */
    /*                                                                                                                        */
    /*      A1  A2  A3  A4  A5  A6  A7  A8                                                                                    */
    /*                                                                                                                        */
    /*  0   01  01  01  02  02  01  02  03                                                                                    */
    /*  1   01  04  02  03  03  02  02  04                                                                                    */
    /*  2   01  06  04  04  05  02  04  06                                                                                    */
    /*  3   02  06  06  05  05  03  04  08                                                                                    */
    /*  4   05  07  08  05  06  05  05  09                                                                                    */
    /*  5   06  07  09  06  07  05  10  09                                                                                    */
    /*  6   06  11  10  07  08  09  11  09                                                                                    */
    /*  7   07  11  10  07  09  09  13  12                                                                                    */
    /*  8   08  12  10  07  10  09  13  12                                                                                    */
    /*  9   09  12  12  08  11  11  13  13                                                                                    */
    /*  10  10  14  13  14  13  12  13  16                                                                                    */
    /*  11  11  16  13  15  14  12  16  17                                                                                    */
    /*  12  12  18  13  16  15  13  16  18                                                                                    */
    /*  13  15  19  14  17  15  14  18  19                                                                                    */
    /*  14  15  20  16  18  17  16  19  22                                                                                    */
    /*  15  18  20  20  18  18  18  19  24                                                                                    */
    /*  16  21  23  23  19  23  22  22  25                                                                                    */
    /*  17  23  23  23  21  23  24  23  25                                                                                    */
    /*  18  23  24  23  21  24  24  23  26                                                                                    */
    /*  19  26  24  25  24  25  24  24  26                                                                                    */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*____                                  _   _                   _               _          _
    |___ /  __      ___ __  ___   ___  __ _| | | |__   __ _ _ __ __| | ___ ___   __| | ___  __| |
      |_ \  \ \ /\ / / `_ \/ __| / __|/ _` | | | `_ \ / _` | `__/ _` |/ __/ _ \ / _` |/ _ \/ _` |
     ___) |  \ V  V /| |_) \__ \ \__ \ (_| | | | | | | (_| | | | (_| | (_| (_) | (_| |  __/ (_| |
    |____/    \_/\_/ | .__/|___/ |___/\__, |_| |_| |_|\__,_|_|  \__,_|\___\___/ \__,_|\___|\__,_|
                     |_|                 |_|
    */

    proc datasets lib=sd1 nolist mt=data mt=view nodetails;delete want; run;quit;

    %utl_submit_wps64x("
    options validvarname=any;
    libname sd1 'd:/sd1';
    proc sql;
        create
           table sd1.want as
        select
            a1.a1
           ,a2.a2
           ,a3.a3
           ,a4.a4
           ,a5.a5
           ,a6.a6
           ,a7.a7
           ,a8.a8
        from
           (select a1 ,monotonic() as seq from %sqlPartition(sd1.provinces,by=a1)) as a1,
           (select a2 ,monotonic() as seq from %sqlPartition(sd1.provinces,by=a2)) as a2,
           (select a3 ,monotonic() as seq from %sqlPartition(sd1.provinces,by=a3)) as a3,
           (select a4 ,monotonic() as seq from %sqlPartition(sd1.provinces,by=a4)) as a4,
           (select a5 ,monotonic() as seq from %sqlPartition(sd1.provinces,by=a5)) as a5,
           (select a6 ,monotonic() as seq from %sqlPartition(sd1.provinces,by=a6)) as a6,
           (select a7 ,monotonic() as seq from %sqlPartition(sd1.provinces,by=a7)) as a7,
           (select a8 ,monotonic() as seq from %sqlPartition(sd1.provinces,by=a8)) as a8
        where
              a1.seq = a1.seq
          and a1.seq = a2.seq
          and a1.seq = a3.seq
          and a1.seq = a4.seq
          and a1.seq = a5.seq
          and a1.seq = a6.seq
          and a1.seq = a7.seq
          and a1.seq = a8.seq
    ;quit;
    proc print;
    run;quit;
    ");

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* The WPS System                                                                                                         */
    /*                                                                                                                        */
    /* Obs    A1    A2    A3    A4    A5    A6    A7    A8                                                                    */
    /*                                                                                                                        */
    /*   1    01    01    01    02    02    01    02    03                                                                    */
    /*   2    01    04    02    03    03    02    02    04                                                                    */
    /*   3    01    06    04    04    05    02    04    06                                                                    */
    /*   4    02    06    06    05    05    03    04    08                                                                    */
    /*   5    05    07    08    05    06    05    05    09                                                                    */
    /*   6    06    07    09    06    07    05    10    09                                                                    */
    /*   7    06    11    10    07    08    09    11    09                                                                    */
    /*   8    07    11    10    07    09    09    13    12                                                                    */
    /*   9    08    12    10    07    10    09    13    12                                                                    */
    /*  10    09    12    12    08    11    11    13    13                                                                    */
    /*  11    10    14    13    14    13    12    13    16                                                                    */
    /*  12    11    16    13    15    14    12    16    17                                                                    */
    /*  13    12    18    13    16    15    13    16    18                                                                    */
    /*  14    15    19    14    17    15    14    18    19                                                                    */
    /*  15    15    20    16    18    17    16    19    22                                                                    */
    /*  16    18    20    20    18    18    18    19    24                                                                    */
    /*  17    21    23    23    19    23    22    22    25                                                                    */
    /*  18    23    23    23    21    23    24    23    25                                                                    */
    /*  19    23    24    23    21    24    24    23    26                                                                    */
    /*  20    26    24    25    24    25    24    24    26                                                                    */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*  _                                           _   _                   _               _          _
    | || |   __      ___ __  ___   _ __   ___  __ _| | | |__   __ _ _ __ __| | ___ ___   __| | ___  __| |
    | || |_  \ \ /\ / / `_ \/ __| | `__| / __|/ _` | | | `_ \ / _` | `__/ _` |/ __/ _ \ / _` |/ _ \/ _` |
    |__   _|  \ V  V /| |_) \__ \ | |    \__ \ (_| | | | | | | (_| | | | (_| | (_| (_) | (_| |  __/ (_| |
       |_|     \_/\_/ | .__/|___/ |_|    |___/\__, |_| |_| |_|\__,_|_|  \__,_|\___\___/ \__,_|\___|\__,_|
                      |_|                        |_|
    */

    proc datasets lib=sd1 nolist mt=data mt=view nodetails;delete want; run;quit;

    %utl_submit_wps64x('
    libname sd1 "d:/sd1";
    proc r;
    export data=sd1.provinces r=provinces;
    submit;
    library(sqldf);
    want<-sqldf("
         select
             a1.a1
            ,a2.a2
            ,a3.a3
            ,a4.a4
            ,a5.a5
            ,a6.a6
            ,a7.a7
            ,a8.a8
         from
             (select a1 ,row_number() over(order by a1) as seq from (select a1 from provinces order by a1)) as a1 inner join
             (select a2 ,row_number() over(order by a2) as seq from (select a2 from provinces order by a2)) as a2 inner join
             (select a3 ,row_number() over(order by a3) as seq from (select a3 from provinces order by a3)) as a3 inner join
             (select a4 ,row_number() over(order by a4) as seq from (select a4 from provinces order by a4)) as a4 inner join
             (select a5 ,row_number() over(order by a5) as seq from (select a5 from provinces order by a5)) as a5 inner join
             (select a6 ,row_number() over(order by a6) as seq from (select a6 from provinces order by a6)) as a6 inner join
             (select a7 ,row_number() over(order by a7) as seq from (select a7 from provinces order by a7)) as a7 inner join
             (select a8 ,row_number() over(order by a8) as seq from (select a8 from provinces order by a8)) as a8
         where
              a1.seq = a2.seq
          and a1.seq = a3.seq
          and a1.seq = a4.seq
          and a1.seq = a5.seq
          and a1.seq = a6.seq
          and a1.seq = a7.seq
          and a1.seq = a8.seq

    ");
    want;
    endsubmit;
    import data=sd1.want r=want;
    ');

    proc print data=sd1.want;
    run;quit;

    /**************************************************************************************************************************/
    /*                                 |                                                                                      */
    /*                                 |                                                                                      */
    /*   The WPS R  System             |     WPS Syatem                                                                       */
    /*                                 |                                                                                      */
    /*      a1 a2 a3 a4 a5 a6 a7 a8    |     A1    A2    A3    A4    A5    A6    A7    A8                                     */
    /*                                 |                                                                                      */
    /*   1  01 01 01 02 02 01 02 03    |     01    01    01    02    02    01    02    03                                     */
    /*   2  01 04 02 03 03 02 02 04    |     01    04    02    03    03    02    02    04                                     */
    /*   3  01 06 04 04 05 02 04 06    |     01    06    04    04    05    02    04    06                                     */
    /*   4  02 06 06 05 05 03 04 08    |     02    06    06    05    05    03    04    08                                     */
    /*   5  05 07 08 05 06 05 05 09    |     05    07    08    05    06    05    05    09                                     */
    /*   6  06 07 09 06 07 05 10 09    |     06    07    09    06    07    05    10    09                                     */
    /*   7  06 11 10 07 08 09 11 09    |     06    11    10    07    08    09    11    09                                     */
    /*   8  07 11 10 07 09 09 13 12    |     07    11    10    07    09    09    13    12                                     */
    /*   9  08 12 10 07 10 09 13 12    |     08    12    10    07    10    09    13    12                                     */
    /*   10 09 12 12 08 11 11 13 13    |     09    12    12    08    11    11    13    13                                     */
    /*   11 10 14 13 14 13 12 13 16    |     10    14    13    14    13    12    13    16                                     */
    /*   12 11 16 13 15 14 12 16 17    |     11    16    13    15    14    12    16    17                                     */
    /*   13 12 18 13 16 15 13 16 18    |     12    18    13    16    15    13    16    18                                     */
    /*   14 15 19 14 17 15 14 18 19    |     15    19    14    17    15    14    18    19                                     */
    /*   15 15 20 16 18 17 16 19 22    |     15    20    16    18    17    16    19    22                                     */
    /*   16 18 20 20 18 18 18 19 24    |     18    20    20    18    18    18    19    24                                     */
    /*   17 21 23 23 19 23 22 22 25    |     21    23    23    19    23    22    22    25                                     */
    /*   18 23 23 23 21 23 24 23 25    |     23    23    23    21    23    24    23    25                                     */
    /*   19 23 24 23 21 24 24 23 26    |     23    24    23    21    24    24    23    26                                     */
    /*   20 26 24 25 24 25 24 24 26    |     26    24    25    24    25    24    24    26                                     */
    /*                                 |                                                                                      */
    /**************************************************************************************************************************/

    /*___                                                 _   _                   _               _          _
    | ___|  __      ___ __  ___   _ __  _   _   ___  __ _| | | |__   __ _ _ __ __| | ___ ___   __| | ___  __| |
    |___ \  \ \ /\ / / `_ \/ __| | `_ \| | | | / __|/ _` | | | `_ \ / _` | `__/ _` |/ __/ _ \ / _` |/ _ \/ _` |
     ___) |  \ V  V /| |_) \__ \ | |_) | |_| | \__ \ (_| | | | | | | (_| | | | (_| | (_| (_) | (_| |  __/ (_| |
    |____/    \_/\_/ | .__/|___/ | .__/ \__, | |___/\__, |_| |_| |_|\__,_|_|  \__,_|\___\___/ \__,_|\___|\__,_|
                     |_|         |_|    |___/          |_|
    */


    %utlfkil(d:/xpt/want.xpt);

    /*----                                                                   ----*/
    /*----  elimiate issues with datastep and view with the same name        ----*/
    /*----                                                                   ----*/

    proc datasets lib=sd1 nolist mt=data mt=view nodetails;delete want; run;quit;
    proc datasets lib=work nolist mt=data mt=view nodetails;delete want want_py_long_names; run;quit;

    %utl_submit_wps64x("
    options validvarname=any lrecl=32756;
    libname sd1 'd:/sd1';
    proc python;
    export data=sd1.provinces  python=provinces;
    submit;
    import pyreadstat as ps;
    from os import path;
    import pandas as pd;
    import numpy as np;
    from pandasql import sqldf;
    mysql = lambda q: sqldf(q, globals());
    from pandasql import PandaSQL;
    pdsql = PandaSQL(persist=True);
    sqlite3conn = next(pdsql.conn.gen).connection.connection;
    sqlite3conn.enable_load_extension(True);
    sqlite3conn.load_extension('c:/temp/libsqlitefunctions.dll');
    mysql = lambda q: sqldf(q, globals());
    want = pdsql('''
         select
             a1.a1
            ,a2.a2
            ,a3.a3
            ,a4.a4
            ,a5.a5
            ,a6.a6
            ,a7.a7
            ,a8.a8
         from
             (select a1 ,row_number() over(order by a1) as seq from (select a1 from provinces order by a1)) as a1 inner join
             (select a2 ,row_number() over(order by a2) as seq from (select a2 from provinces order by a2)) as a2 inner join
             (select a3 ,row_number() over(order by a3) as seq from (select a3 from provinces order by a3)) as a3 inner join
             (select a4 ,row_number() over(order by a4) as seq from (select a4 from provinces order by a4)) as a4 inner join
             (select a5 ,row_number() over(order by a5) as seq from (select a5 from provinces order by a5)) as a5 inner join
             (select a6 ,row_number() over(order by a6) as seq from (select a6 from provinces order by a6)) as a6 inner join
             (select a7 ,row_number() over(order by a7) as seq from (select a7 from provinces order by a7)) as a7 inner join
             (select a8 ,row_number() over(order by a8) as seq from (select a8 from provinces order by a8)) as a8
         where
              a1.seq = a2.seq
          and a1.seq = a3.seq
          and a1.seq = a4.seq
          and a1.seq = a5.seq
          and a1.seq = a6.seq
          and a1.seq = a7.seq
          and a1.seq = a8.seq
    ''');
    print(want);
    ps.write_xport(want,'d:\\xpt\\want.xpt',table_name='want',file_format_version=5
    ,column_labels=['a1','a2','a3','a4','a5','a6','a7','a8']);
    endsubmit;
    ");

    /*--- handles long variable names by using the label to rename the variables  ----*/
    options label;
    libname xpt xport "d:/xpt/want.xpt";
    proc contents data=xpt._all_;
    run;quit;

    data want_py_long_names;
      %utl_rens(xpt.want) ;
      set want;
    run;quit;
    libname xpt clear;

    proc print data=want_py_long_names;
    run;quit;

    /**************************************************************************************************************************/
    /*                                        |                                                                               */
    /*  The WPS PYTHON Procedure              |     WPS System                                                                */
    /*                                        |                                                                               */
    /*      A1  A2  A3  A4  A5  A6  A7  A8    |     A1    A2    A3    A4    A5    A6    A7    A8                              */
    /*                                        |                                                                               */
    /*  0   01  01  01  02  02  01  02  03    |     01    01    01    02    02    01    02    03                              */
    /*  1   01  04  02  03  03  02  02  04    |     01    04    02    03    03    02    02    04                              */
    /*  2   01  06  04  04  05  02  04  06    |     01    06    04    04    05    02    04    06                              */
    /*  3   02  06  06  05  05  03  04  08    |     02    06    06    05    05    03    04    08                              */
    /*  4   05  07  08  05  06  05  05  09    |     05    07    08    05    06    05    05    09                              */
    /*  5   06  07  09  06  07  05  10  09    |     06    07    09    06    07    05    10    09                              */
    /*  6   06  11  10  07  08  09  11  09    |     06    11    10    07    08    09    11    09                              */
    /*  7   07  11  10  07  09  09  13  12    |     07    11    10    07    09    09    13    12                              */
    /*  8   08  12  10  07  10  09  13  12    |     08    12    10    07    10    09    13    12                              */
    /*  9   09  12  12  08  11  11  13  13    |     09    12    12    08    11    11    13    13                              */
    /*  10  10  14  13  14  13  12  13  16    |     10    14    13    14    13    12    13    16                              */
    /*  11  11  16  13  15  14  12  16  17    |     11    16    13    15    14    12    16    17                              */
    /*  12  12  18  13  16  15  13  16  18    |     12    18    13    16    15    13    16    18                              */
    /*  13  15  19  14  17  15  14  18  19    |     15    19    14    17    15    14    18    19                              */
    /*  14  15  20  16  18  17  16  19  22    |     15    20    16    18    17    16    19    22                              */
    /*  15  18  20  20  18  18  18  19  24    |     18    20    20    18    18    18    19    24                              */
    /*  16  21  23  23  19  23  22  22  25    |     21    23    23    19    23    22    22    25                              */
    /*  17  23  23  23  21  23  24  23  25    |     23    23    23    21    23    24    23    25                              */
    /*  18  23  24  23  21  24  24  23  26    |     23    24    23    21    24    24    23    26                              */
    /*  19  26  24  25  24  25  24  24  26    |     26    24    25    24    25    24    24    26                              */
    /*                                        |                                                                               */
    /**************************************************************************************************************************/


    /*__                              _       _            _
     / /_   __      ___ __  ___    __| | __ _| |_ __ _ ___| |_ ___ _ __
    | `_ \  \ \ /\ / / `_ \/ __|  / _` |/ _` | __/ _` / __| __/ _ \ `_ \
    | (_) |  \ V  V /| |_) \__ \ | (_| | (_| | || (_| \__ \ ||  __/ |_) |
     \___/    \_/\_/ | .__/|___/  \__,_|\__,_|\__\__,_|___/\__\___| .__/
                     |_|                                          |_|
    */


    proc datasets lib=sd1 mt=data mt=view nolist nodetails; delete a:;run;quit;
    proc datasets lib=work mt=data mt=view nolist nodetails; delete want a:;run;quit;


    proc sql;
      select
         nvar
        ,nlobs
      into
         :_nvar   trimmed
        ,:_nlobs  trimmed
      from
        sashelp.vtable
      where
        memname eqt 'PROVINCES'
    ;quit;

    %let _obsvar = %eval(&_nlobs*&_nvar);

    %put &_nvar  ;
    %put &_nlobs ;
    %put &_obsvar;

    data want;
         /*----                                                              ----*/
         /*----  Could not get temporary arrays to work                      ----*/
         /*----                                                              ----*/

         array row[8] $2 %utl_varlist(sd1.provinces); /*---- original data   ----*/
         array inary[20,8]   $2 x1-x&_obsvar;

         array rotate[8 ,20] $2 t1-t&_obsvar;
         array final[8 ,20]  $2 f1-f&_obsvar;

         array col[20]       $2 s1-s&_nlobs;

         do i = 1 to &_nlobs;                 /*---- load data into inarray       ----*/

           set sd1.provinces ;

           do j=1 to &_NVAR;
               inary[i,j] = row[j];
           end;
         end;

         /*----                                                              ----*/
         /*---- ROTATE 90 DEGREES                                            ----*/
         /*----                                                              ----*/

         do j=1 to &_nvar;
           do i = 1 to &_nlobs;
               rotate[j,i] = inary[i,j];
               col[i]  = rotate[j,i];    /*---- sortc only works 1dim arrays ----*/
           end;

           call sortc(of col(*));        /*---- sortc only works 1 dim arrays----*/

           /* COLUMNS SORTED JUST MEED TO ROTATE AND OUPUT
           S1 S2 S3 S4 S5 S6 S7 S8 S9 S10 S11 S12 S13 S14 S15 S16 S17 S18 S19 S20

           BI EQ FH FK GO HA IG JK KD LF  OB  OU  QD  RE  UI  WE  WY  YE  YO  ZW
           AR DT FE FP GW GZ KI KL LH LV  NV  PO  RD  SZ  TK  TY  WG  WQ  XV  XW
           AK BV DA FL HK IK JJ JJ JR LJ  MH  MQ  MX  NV  PL  TT  WK  WU  WW  YM
           */

           k=j;
           do l=1 to &_nlobs;

             final[k,l]=col[l];

           end;

           do m=1 to &_nlobs;

            do n=1 to &_nvar;

             row[n ]=final[n,m ];  /*---- make long and add a1-a8            ----*/

            end;

             if j=&_nvar then output;   /*---- finished when a8                   ----*/
           end;

         end;

         stop;

         keep a1-a&_nvar;

    run;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* WORK.WANT total obs=20                                                                                                 */
    /*                                                                                                                        */
    /* Obs    A1    A2    A3    A4    A5    A6    A7    A8                                                                    */
    /*                                                                                                                        */
    /*   1    01    01    01    02    02    01    02    03                                                                    */
    /*   2    01    04    02    03    03    02    02    04                                                                    */
    /*   3    01    06    04    04    05    02    04    06                                                                    */
    /*   4    02    06    06    05    05    03    04    08                                                                    */
    /*   5    05    07    08    05    06    05    05    09                                                                    */
    /*   6    06    07    09    06    07    05    10    09                                                                    */
    /*   7    06    11    10    07    08    09    11    09                                                                    */
    /*   8    07    11    10    07    09    09    13    12                                                                    */
    /*   9    08    12    10    07    10    09    13    12                                                                    */
    /*  10    09    12    12    08    11    11    13    13                                                                    */
    /*  11    10    14    13    14    13    12    13    16                                                                    */
    /*  12    11    16    13    15    14    12    16    17                                                                    */
    /*  13    12    18    13    16    15    13    16    18                                                                    */
    /*  14    15    19    14    17    15    14    18    19                                                                    */
    /*  15    15    20    16    18    17    16    19    22                                                                    */
    /*  16    18    20    20    18    18    18    19    24                                                                    */
    /*  17    21    23    23    19    23    22    22    25                                                                    */
    /*  18    23    23    23    21    23    24    23    25                                                                    */
    /*  19    23    24    23    21    24    24    23    26                                                                    */
    /*  20    26    24    25    24    25    24    24    26                                                                    */
    /*                                                                                                                        */
    /**************************************************************************************************************************/


    /*____                                       _
    |___  | __      ___ __  ___   ___  ___  _ __| |_   _ __ ___   ___ _ __ __ _  ___
       / /  \ \ /\ / / `_ \/ __| / __|/ _ \| `__| __| | `_ ` _ \ / _ \ `__/ _` |/ _ \
      / /    \ V  V /| |_) \__ \ \__ \ (_) | |  | |_  | | | | | |  __/ | | (_| |  __/
     /_/      \_/\_/ | .__/|___/ |___/\___/|_|   \__| |_| |_| |_|\___|_|  \__, |\___|
                     |_|                                                  |___/


    */

    proc datasets lib=sd1 nolist mt=data mt=view nodetails;delete want; run;quit;
    proc datasets lib=work mt=data mt=view nolist nodetails; delete want a:;run;quit;

    proc sql;
     create view a1 as select a1 from sd1.provinces order by a1;
     create view a2 as select a2 from sd1.provinces order by a2;
     create view a3 as select a3 from sd1.provinces order by a3;
     create view a4 as select a4 from sd1.provinces order by a4;
     create view a5 as select a5 from sd1.provinces order by a5;
     create view a6 as select a6 from sd1.provinces order by a6;
     create view a7 as select a7 from sd1.provinces order by a7;
     create view a8 as select a8 from sd1.provinces order by a8;
    quit;

    data sd1.want;
      merge a:;
    run;quit;


    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* WORK.WANT total obs=20                                                                                                 */
    /*                                                                                                                        */
    /* Obs    A1    A2    A3    A4    A5    A6    A7    A8                                                                    */
    /*                                                                                                                        */
    /*   1    01    01    01    02    02    01    02    03                                                                    */
    /*   2    01    04    02    03    03    02    02    04                                                                    */
    /*   3    01    06    04    04    05    02    04    06                                                                    */
    /*   4    02    06    06    05    05    03    04    08                                                                    */
    /*   5    05    07    08    05    06    05    05    09                                                                    */
    /*   6    06    07    09    06    07    05    10    09                                                                    */
    /*   7    06    11    10    07    08    09    11    09                                                                    */
    /*   8    07    11    10    07    09    09    13    12                                                                    */
    /*   9    08    12    10    07    10    09    13    12                                                                    */
    /*  10    09    12    12    08    11    11    13    13                                                                    */
    /*  11    10    14    13    14    13    12    13    16                                                                    */
    /*  12    11    16    13    15    14    12    16    17                                                                    */
    /*  13    12    18    13    16    15    13    16    18                                                                    */
    /*  14    15    19    14    17    15    14    18    19                                                                    */
    /*  15    15    20    16    18    17    16    19    22                                                                    */
    /*  16    18    20    20    18    18    18    19    24                                                                    */
    /*  17    21    23    23    19    23    22    22    25                                                                    */
    /*  18    23    23    23    21    23    24    23    25                                                                    */
    /*  19    23    24    23    21    24    24    23    26                                                                    */
    /*  20    26    24    25    24    25    24    24    26                                                                    */
    /*                                                                                                                        */
    /**************************************************************************************************************************/


    /*___     __       _ _          _   _               _
     ( _ )   / _| __ _(_) | ___  __| | | |__   __ _ ___| |__
     / _ \  | |_ / _` | | |/ _ \/ _` | | `_ \ / _` / __| `_ \
    | (_) | |  _| (_| | | |  __/ (_| | | | | | (_| \__ \ | | |
     \___/  |_|  \__,_|_|_|\___|\__,_| |_| |_|\__,_|___/_| |_|

    */

    data sd1.want;
        if 0 then set sd1.provinces(keep = a1 a2);
        declare hash h(ordered:'d');
        rc = h.definekey('_n_');
        rc = h.definedata('a1','a2');
        rc = h.definedone();
        declare hiter hi('h');
        do _n_ = 1 by 1 until(eof1);
            set sd1.provinces(keep =a1 a2) end = eof1;
            rc = h.add();
        end;
        do until(eof2);
            set sd1.provinces(keep=a1 a2) end = eof2;
            rc = hi.next();
            put a1 a2;
            output;
            drop rc;
        end;
    run;

    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */
