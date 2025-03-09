# utl-ninetyfifth-percentiles-of-home-runs-for-lf-and-rf-baseball-players-sas-and-sql-r-excel
Ninetyfifth percentiles of home runs for lf and rf baseball players sas and sql r python excel
    %let pgm=utl-ninetyfifth-percentiles-of-home-runs-for-lf-and-rf-baseball-players-sas-and-sql-r-excel;

    %stop_submission;

    Ninetyfifth percentiles of home runs for lf and rf baseball players sas and sql r python excel

    Hypothesis Tested

         Right Fielders hit significantly more homeruns then left fielders at alpha=0.05

         FOUR SOLUTIONS

              1 sas proc univariate
              2 r sql postgres sql
                SOAPBOX ON (beware my soap boxes are my observations and may not always be correct?)
                  sqldf does not have a percentile function
                  I could not compile the extension or get dbCreateFubction to work)
                  In addition postgres is case sensitive and seems to work better with lower case names?
                SOAPBOX OFF
              3 test hypothesis  (Left Feilders have more home runs then right fiedlders)
              4 excel solution not shown (put r code in)
                https://tinyurl.com/3cmsbznd

    github
    https://tinyurl.com/5yfnf9f5
    https://github.com/rogerjdeangelis/utl-ninetyfifth-percentiles-of-home-runs-for-lf-and-rf-baseball-players-sas-and-sql-r-excel

    related to
    https://tinyurl.com/3f99zuz2
    https://communities.sas.com/t5/SAS-Programming/Output-all-values-greater-than-90th-percentile/m-p/953589#M372534

    /****************************************************************************************************************************/
    /*                          |                                        |                                                      */
    /*           INPUT          |      PROCESS                           |  OUTPUT                                              */
    /*           =====          |                                        |                                                      */
    /*                          |                                        |                                                      */
    /*  POS HOMERS   POS HOMERS | 1 SAS PROC UNIVARIATE                  | SEM = standard error of mean used for CIs=std/n**.5  */
    /*                          | =====================                  |                                                      */
    /*  RF    20      LF   10   |                                        | WANT                                                 */
    /*  RF    13      LF   04   | proc univariate data=sd1.have plot ;   |                                                      */
    /*  LF    20      LF   06   |   class pos;                           |                                                      */
    /*  RF    13      RF   02   |   var homers;                          | POS N   MEAN    STD   SEM   MED    P_95              */
    /*  LF    01      LF   20   |   output out=want mean=avg median=med  |                                                      */
    /*  RF    26      RF   20   |          pctlpts=95 std=std stderr=sem | LF  25  12.8    9.2   1.84  10      31               */
    /*  RF    07      RF   28   |          pctlpre=p_ n=obs;             | RF  26  20.1    9.2   1.81  20      33               */
    /*  RF    31      RF   12   | run;quit;                              |                                                      */
    /*  LF    16      RF   11   |                                        |                                                      */
    /*  RF    27      LF   08   |                                        |                                                      */
    /*  RF    25      RF   19   | 2 R SQL POSTGRES SQL                   | 3 TEST HYPOTHESIS (LF HOMERS>+ RF HOMERS)            */
    /*  RF    24      LF   18   | ====================                   | ==========================================           */
    /*  LF    27      RF   19   |                                        |                                                      */
    /*  LF    23      LF   09   | proc datasets lib=sd1 nolist nodetails | Null hypothesis H0:                                  */
    /*  LF    31      RF   04   |  delete want;                          |   Left Fielders Home Run Count                       */
    /*  LF    04      LF   12   | run;quit;                              |    is greater than or equal                          */
    /*  LF    14      RF   30   |                                        |    to Right Home Run counts.                         */
    /*  LF    21      LF   07   | %utl_rbeginx;                          |                                                      */
    /*  LF    11      RF   33   | parmcards4;                            | At the 0.05 significance level,                      */
    /*  LF    05      RF   17   | library(RPostgres)                     |  we reject the null hypothesis conclude that         */
    /*  RF    15      LF   09   | library(DBI)                           |  Right Fielders have a significantly                 */
    /*  RF    21      RF   23   | library(haven)                         |  higher home run count then Left Fielders            */
    /*  RF    40      RF   14   | source("c:/oto/fn_tosas9x.R");         |                                                      */
    /*  LF    02      LF   09   | have<-read_sas("d:/sd1/have.sas7bdat") | proc ttest data=sd1.have sides=L alpha=0.05;         */
    /*  LF    33      LF   01   | colnames(have)<-c("pos","homer")       |    class pos;                                        */
    /*  RF    29                | have                                   |    var homers;                                       */
    /*                          | con <- dbConnect(                      | run;                                                 */
    /*  libname sd1 "d:/sd1";   |    RPostgres::Postgres()               |                                                      */
    /*  data sd1.have;          |    ,dbname = "devel"                   | TEST                                                 */
    /*  informat pos $2.        |    ,host = "localhost"                 | ====                                                 */
    /*    HOMERS 2.;            |    ,port = 5432                        | Method Variances  DF t Value  Pr < t                 */
    /*  input POS HOMERS @@;    |    ,user = "postgres"                  |                                                      */
    /*  CARDS4;                 |    ,password = "12345678")             | Pooled Equal      49   -2.82  0.0035                 */
    /*  RF 20 LF 10             | dbWriteTable(con                       |                                                      */
    /*  RF 13 LF 04             |     ,"havsql"                          | EQUALITY OF VARIANCES                                */
    /*  LF 20 LF 06             |     ,have                              | =====================                                */
    /*  RF 13 RF 02             |     ,row.names = FALSE                 | Method   Num DF  Den DF   F Value    Pr > F          */
    /*  LF 01 LF 20             |     ,overwrite = TRUE)                 |                                                      */
    /*  RF 26 RF 20             | query <- "                             | Folded F     25      24      1.01    0.9773          */
    /*  RF 07 RF 28             |    select                              |                                                      */
    /*  RF 31 RF 12             |      pos                               |                                                      */
    /*  LF 16 RF 11             |     ,percentile_disc(0.95)             |                     POSITION                         */
    /*  RF 27 LF 08             |       within group                     |                 LF          RF                       */
    /*  RF 25 RF 19             |       (order by havsql.homer) as p90   |          -----+-----------+----------                */
    /*  RF 24 LF 18             | from                                   |         |   25 obs     26 obs       |                */
    /*  LF 27 RF 19             |      havsql                            |         |               40          |                */
    /*  LF 23 LF 09             | group                                  |      40 +                |          + 40             */
    /*  LF 31 RF 04             |     by pos                             |         |                |          |                */
    /*  LF 04 LF 12             | ;"                                     |         |                |          |                */
    /*  LF 14 RF 30             | want <- dbGetQuery(con, query)         |         |                |          |                */
    /*  LF 21 LF 07             | want                                   |      35 +                |          + 35             */
    /*  LF 11 RF 33             | dbDisconnect(con)                      |         |   33           |          |                */
    /*  LF 05 RF 17             | print(want)                            |         |    |           |          |                */
    /*  RF 15 LF 09             | fn_tosas9x(                            |         |    |           |          |                */
    /*  RF 21 RF 23             |       inp    = want                    |    H 30 +----|-----------|-------   + 30  H          */
    /*  RF 40 RF 14             |      ,outlib ="d:/sd1/"                |    O    |    |           |          |     O          */
    /*  LF 02 LF 09             |      ,outdsn ="want"                   |    M    |    |        +-----+ 27    |     M          */
    /*  LF 33 LF 01             |      );                                |    E    |    |        |     |       |     E          */
    /*  RF 29                   |                                        |      25 +    |        |     |       + 25             */
    /*  ;;;;                    | ;;;;                                   |    R    |    |        |     |       |     R          */
    /*  run;quit;               | %utl_rendx;                            |    U    |    |        |     |       |     U          */
    /*                          |                                        |    N    |    |        |     |Median |     N          */
    /*                          | proc print data=sd1.want;              |    S 20 + +-----+ 20  *-20--*Mean   + 20  S          */
    /*                          | run;quit;                              |         | |     |     |     |       |                */
    /*                          |                                        |         | |     |     |     |       |                */
    /*                          |                                        |         | |     |     |     |       |                */
    /*                          |                                        |      15 + |     |     |     |       + 15             */
    /*                          |                                        |         | |     |     |     |       |                */
    /*                          |                                        |         | | 13  |mean +-----+ 13    |                */
    /*                          |                                        |         | |     |        |          |                */
    /*                          |                                        |      10 + *-10--*mediann |          + 10             */
    /*                          |                                        |         | |     |        |          |                */
    /*                          |                                        |         | |     |        |          |                */
    /*                          |                                        |         | +-----+ 6      |          |                */
    /*                          |                                        |       5 +    |           |          +  5             */
    /*                          |                                        |         |    |           |          |                */
    /*                          |                                        |         |    |           |          |                */
    /*                          |                                        |         |    |           2          |                */
    /*                          |                                        |       0 +    1                      +  0             */
    /*                          |                                        |          ----+-----------+----------                 */
    /*                          |                                        |              LF          RF                          */
    /*                          |                                        |                POSITION                              */
    /*                          |                                        |                                                      */
    /*                          |                                        |    Very slight overlap of 95% CI                     */
    /*                          |                                        |                                                      */
    /*                          |                                        |                95% CI (USING T DISTRIBUTION)         */
    /*                          |                                        |       10            15            20            25   */
    /*                          |                                        |   ------+-------------+-------------+------------+   */
    /*                          |                                        |   |    95% Confidence Interval of the Mean       |   */
    /*                          |                                        |   |                                              |   */
    /*                          |                                        |   |                     16.4                  24 |   */
    /*                          |                                        | RF+                       |--------20----------| +RF */
    /*                          |                                        |   |                                              |   */
    /*                          |                                        |   | 9.0                  16.6                    |   */
    /*                          |                                        | LF+  |---------13----------| 16.6                +LF */
    /*                          |                                        |   |                                              |   */
    /*                          |                                        |   ------+-------------+-------------+------------+   */
    /*                          |                                        |        10            15            20           25   */
    /*                          |                                        |                             95% CI                   */
    /*                          |                                        |                                                      */
    /*                          |                                        | Since sample are almost = 25/26 we can compare bins  */
    /*                          |                                        |                                                      */
    /*                          |                                        |        2   4   6   8    2   4   6   8  10            */
    /*                          |                                        |      --+---+---+---+   -+---+---+---+---+------      */
    /*                          |                                        |     | LEFT FIELD        RIGHT FIELD            |     */
    /*                          |                                        |     |                                          |     */
    /*                          |                                        |     |           PERCENT                PERCENT |     */
    /*                          |                                        |     |                                          |BINS */
    /*                          |                                        | BINS| **            2.0                    0.0 |39   */
    /*                          |                                        |   33| ******        5.9 ****               3.9 |33   */
    /*                          |                                        |   27| ************ 11.8 **                 2.0 |27   */
    /*                          |                                        |   21| ************ 11.8 **********         9.8 |21   */
    /*                          |                                        |   15| ************ 11.8 ******             5.9 |15   */
    /*                          |                                        |    9| ****          3.9 ****************  15.7 | 9   */
    /*                          |                                        |    3| ****          3.9 ************      11.8 | 3   */
    /*                          |                                        |      --+---+---+---+   -+---+---+---+---+-------     */
    /*                          |                                        |        2   4   6   8    2   4   6   8  10            */
    /*                          |                                        |        HOME RUNS             HOME RUNS               */
    /*                          |                                        |       LEFT FEILD            RIGHT FEILD              */
    /*                          |                                        |                                                      */
    /*                          |                                        | PROCESS                                              */
    /*                          |                                        |                                                      */
    /*                          |                                        | proc ttest data=sd1.have sides=L alpha=0.05;         */
    /*                          |                                        |    class pos;                                        */
    /*                          |                                        |    var homers;                                       */
    /*                          |                                        | run;                                                 */
    /*                          |                                        |                                                      */
    /*                          |                                        | *Check T value;                                      */
    /*                          |                                        | data p_value;                                        */
    /*                          |                                        |     t_value = -2.82;                                 */
    /*                          |                                        |     df = 49;                                         */
    /*                          |                                        |     p_value_one_tailed =                             */
    /*                          |                                        |       1 - probt(abs(t_value), df);                   */
    /*                          |                                        | run;                                                 */
    /*                          |                                        |                                                      */
    /*                          |                                        |                    P_VALUE_                          */
    /*                          |                                        |  T_VALUE    DF    ONE_TAILED                         */
    /*                          |                                        |                                                      */
    /*                          |                                        |   -2.82     49     .0034564                          */
    /*                          |                                        |                                                      */
    /****************************************************************************************************************************/

    /*                   _
    (_)_ __  _ __  _   _| |_
    | | `_ \| `_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    */


    libname sd1 "d:/sd1";
    data sd1.have;
    informat pos $2.
      HOMERS 2.;
    input POS HOMERS @@;
    CARDS4;
    RF 20 LF 10
    RF 13 LF 04
    LF 20 LF 06
    RF 13 RF 02
    LF 01 LF 20
    RF 26 RF 20
    RF 07 RF 28
    RF 31 RF 12
    LF 16 RF 11
    RF 27 LF 08
    RF 25 RF 19
    RF 24 LF 18
    LF 27 RF 19
    LF 23 LF 09
    LF 31 RF 04
    LF 04 LF 12
    LF 14 RF 30
    LF 21 LF 07
    LF 11 RF 33
    LF 05 RF 17
    RF 15 LF 09
    RF 21 RF 23
    RF 40 RF 14
    LF 02 LF 09
    LF 33 LF 01
    RF 29
    ;;;;
    run;quit;

    /**************************************************************************************************************************/
    /*  POS HOMERS   POS HOMERS                                                                                               */
    /*                                                                                                                        */
    /*  RF    20      LF   10                                                                                                 */
    /*  RF    13      LF   04                                                                                                 */
    /*  LF    20      LF   06                                                                                                 */
    /*  RF    13      RF   02                                                                                                 */
    /*  LF    01      LF   20                                                                                                 */
    /*  RF    26      RF   20                                                                                                 */
    /*  RF    07      RF   28                                                                                                 */
    /*  RF    31      RF   12                                                                                                 */
    /*  LF    16      RF   11                                                                                                 */
    /*  RF    27      LF   08                                                                                                 */
    /*  RF    25      RF   19                                                                                                 */
    /*  RF    24      LF   18                                                                                                 */
    /*  LF    27      RF   19                                                                                                 */
    /*  LF    23      LF   09                                                                                                 */
    /*  LF    31      RF   04                                                                                                 */
    /*  LF    04      LF   12                                                                                                 */
    /*  LF    14      RF   30                                                                                                 */
    /*  LF    21      LF   07                                                                                                 */
    /*  LF    11      RF   33                                                                                                 */
    /*  LF    05      RF   17                                                                                                 */
    /*  RF    15      LF   09                                                                                                 */
    /*  RF    21      RF   23                                                                                                 */
    /*  RF    40      RF   14                                                                                                 */
    /*  LF    02      LF   09                                                                                                 */
    /*  LF    33      LF   01                                                                                                 */
    /*  RF    29                                                                                                              */
    /**************************************************************************************************************************/

    /*                                                     _                 _       _
    / |  ___  __ _ ___   _ __  _ __ ___   ___  _   _ _ __ (_)_   ____ _ _ __(_) __ _| |_ ___
    | | / __|/ _` / __| | `_ \| `__/ _ \ / __|| | | | `_ \| \ \ / / _` | `__| |/ _` | __/ _ \
    | | \__ \ (_| \__ \ | |_) | | | (_) | (__ | |_| | | | | |\ V / (_| | |  | | (_| | ||  __/
    |_| |___/\__,_|___/ | .__/|_|  \___/ \___| \__,_|_| |_|_| \_/ \__,_|_|  |_|\__,_|\__\___|
                        |_|
    */

    proc univariate data=sd1.have plot ;
      class pos;
      var homers;
      output out=want mean=avg median=med
             pctlpts=95 std=std stderr=sem
             pctlpre=p_ n=obs;
    run;quit;


    /**************************************************************************************************************************/
    /* Obs    POS    OBS      AVG        STD        SEM      MED    P_95                                                      */
    /*                                                                                                                        */
    /*  1     LF      25    12.8400    9.18187    1.83637     10     31                                                       */
    /*  2     RF      26    20.1154    9.24046    1.81220     20     33                                                       */
    /**************************************************************************************************************************/

    /*___                     _                   _                                 _
    |___ \   _ __   ___  __ _| |  _ __   ___  ___| |_ __ _ _ __ ___  ___  ___  __ _| |
      __) | | `__| / __|/ _` | | | `_ \ / _ \/ __| __/ _` | `__/ _ \/ __|/ __|/ _` | |
     / __/  | |    \__ \ (_| | | | |_) | (_) \__ \ || (_| | | |  __/\__ \\__ \ (_| | |
    |_____| |_|    |___/\__, |_| | .__/ \___/|___/\__\__, |_|  \___||___/|___/\__, |_|
                           |_|   |_|                 |___/                       |_|
    */

    proc datasets lib=sd1 nolist nodetails
     delete want;
    run;quit;

    %utl_rbeginx;
    parmcards4;
    library(RPostgres)
    library(DBI)
    library(haven)
    source("c:/oto/fn_tosas9x.R");
    have<-read_sas("d:/sd1/have.sas7bdat")
    colnames(have)<-c("pos","homer")
    have
    con <- dbConnect(
       RPostgres::Postgres()
       ,dbname = "devel"
       ,host = "localhost"
       ,port = 5432
       ,user = "postgres"
       ,password = "12345678")
    dbWriteTable(con
        ,"havsql"
        ,have
        ,row.names = FALSE
        ,overwrite = TRUE)
    query <- "
       select
         pos
        ,percentile_disc(0.95)
          within group
          (order by havsql.homer) as p90
    from
         havsql
    group
        by pos
    ;"
    want <- dbGetQuery(con, query)
    want
    dbDisconnect(con)
    print(want)
    fn_tosas9x(
          inp    = want
         ,outlib ="d:/sd1/"
         ,outdsn ="want"
         );
    ;;;;
    %utl_rendx;

    proc print data=sd1.want;
    run;quit;

    /*
    | | ___   __ _
    | |/ _ \ / _` |
    | | (_) | (_| |
    |_|\___/ \__, |
             |___/
    */

    > library(RPostgres)
    > library(DBI)
    > library(haven)
    > source("c:/oto/fn_tosas9x.R");
    > have<-read_sas("d:/sd1/have.sas7bdat")
    > colnames(have)<-c("pos","homer")
    > have
    # A tibble: 51 Ã— 2
       pos   homer
       <chr> <dbl>
     1 RF       20
     2 LF       10
     3 RF       13
     4 LF        4
     5 LF       20
     6 LF        6
     7 RF       13
     8 RF        2
     9 LF        1
    10 LF       20
    # â„¹ 41 more rows
    > con <- dbConnect(
    +    RPostgres::Postgres()
    +    ,dbname = "devel"
    +    ,host = "localhost"
    +    ,port = 5432
    +    ,user = "postgres"
    +    ,password = "12345678")
    > dbWriteTable(con
    +     ,"havsql"
    +     ,have
    +     ,row.names = FALSE
    +     ,overwrite = TRUE)
    > query <- "
    +    select
    +      pos
    +     ,percentile_disc(0.95)
    +       within group
    +       (order by havsql.homer) as p90
    + from
    +      havsql
    + group
    +     by pos
    + ;"
    > want <- dbGetQuery(con, query)
    > want
      pos p90
    1  LF  31
    2  RF  33
    > dbDisconnect(con)

    > print(want)
      pos p90
    1  LF  31
    2  RF  33

    > fn_tosas9x(
    +       inp    = want
    +      ,outlib ="d:/sd1/"
    +      ,outdsn ="want"
    +      );
    >
    NOTE: 61 lines were written to file PRINT.

    2 cases were transferred(0.00 seconds)


    /**************************************************************************************************************************/
    /* R           | SAS                                                                                                      */
    /*    POS P90  | ROWNAMES    POS    P90                                                                                   */
    /*             |                                                                                                          */
    /*  1  LF  31  |     1       LF      31                                                                                   */
    /*  2  RF  33  |     2       RF      33                                                                                   */
    /**************************************************************************************************************************/

    /*____   _            _     _                       _   _               _
    |___ /  | |_ ___  ___| |_  | |__  _   _ _ __   ___ | |_| |__   ___  ___(_)___
      |_ \  | __/ _ \/ __| __| | `_ \| | | | `_ \ / _ \| __| `_ \ / _ \/ __| / __|
     ___) | | ||  __/\__ \ |_  | | | | |_| | |_) | (_) | |_| | | |  __/\__ \ \__ \
    |____/   \__\___||___/\__| |_| |_|\__, | .__/ \___/ \__|_| |_|\___||___/_|___/
                                      |___/|_|
    */

    proc ttest data=sd1.have sides=L alpha=0.05;
       class pos;
       var homers;
    run;

    *Check T value;
    data p_value;
        t_value = -2.82;
        df = 49;
        p_value_one_tailed =
          1 - probt(abs(t_value), df);
    run;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*  Null hypothesis H0:                                                                                                   */
    /*    Left Fielders Home Run Count                                                                                        */
    /*     is greater than or equal                                                                                           */
    /*     to Right Home Run counts.                                                                                          */
    /*                                                                                                                        */
    /*  At the 0.05 significance level,                                                                                       */
    /*   we reject the null hypothesis conclude that                                                                          */
    /*   Right Fielders have a significantly                                                                                  */
    /*   higher home run count then Left Fielders                                                                             */
    /*                                                                                                                        */
    /*  proc ttest data=sd1.have sides=L alpha=0.05;                                                                          */
    /*     class pos;                                                                                                         */
    /*     var homers;                                                                                                        */
    /*  run;                                                                                                                  */
    /*                                                                                                                        */
    /*  TEST                                                                                                                  */
    /*  ====                                                                                                                  */
    /*  Method Variances  DF t Value  Pr < t                                                                                  */
    /*                                                                                                                        */
    /*  Pooled Equal      49   -2.82  0.0035                                                                                  */
    /*                                                                                                                        */
    /*  EQUALITY OF VARIANCES                                                                                                 */
    /*  =====================                                                                                                 */
    /*  Method   Num DF  Den DF   F Value    Pr > F                                                                           */
    /*                                                                                                                        */
    /*  Folded F     25      24      1.01    0.9773                                                                           */
    /*                                                                                                                        */
    /*                                                                                                                        */
    /*                      POSITION                                                                                          */
    /*                  LF          RF                                                                                        */
    /*           -----+-----------+----------                                                                                 */
    /*          |   25 obs     26 obs       |                                                                                 */
    /*          |               40          |                                                                                 */
    /*       40 +                |          + 40                                                                              */
    /*          |                |          |                                                                                 */
    /*          |                |          |                                                                                 */
    /*          |                |          |                                                                                 */
    /*       35 +                |          + 35                                                                              */
    /*          |   33           |          |                                                                                 */
    /*          |    |           |          |                                                                                 */
    /*          |    |           |          |                                                                                 */
    /*     H 30 +----|-----------|-------   + 30  H                                                                           */
    /*     O    |    |           |          |     O                                                                           */
    /*     M    |    |        +-----+ 27    |     M                                                                           */
    /*     E    |    |        |     |       |     E                                                                           */
    /*       25 +    |        |     |       + 25                                                                              */
    /*     R    |    |        |     |       |     R                                                                           */
    /*     U    |    |        |     |       |     U                                                                           */
    /*     N    |    |        |     |Median |     N                                                                           */
    /*     S 20 + +-----+ 20  *-20--*Mean   + 20  S                                                                           */
    /*          | |     |     |     |       |                                                                                 */
    /*          | |     |     |     |       |                                                                                 */
    /*          | |     |     |     |       |                                                                                 */
    /*       15 + |     |     |     |       + 15                                                                              */
    /*          | |     |     |     |       |                                                                                 */
    /*          | | 13  |mean +-----+ 13    |                                                                                 */
    /*          | |     |        |          |                                                                                 */
    /*       10 + *-10--*mediann |          + 10                                                                              */
    /*          | |     |        |          |                                                                                 */
    /*          | |     |        |          |                                                                                 */
    /*          | +-----+ 6      |          |                                                                                 */
    /*        5 +    |           |          +  5                                                                              */
    /*          |    |           |          |                                                                                 */
    /*          |    |           |          |                                                                                 */
    /*          |    |           2          |                                                                                 */
    /*        0 +    1                      +  0                                                                              */
    /*           ----+-----------+----------                                                                                  */
    /*               LF          RF                                                                                           */
    /*                 POSITION                                                                                               */
    /*                                                                                                                        */
    /*     Very slight overlap of 95% CI                                                                                      */
    /*                                                                                                                        */
    /*                 95% CI (USING T DISTRIBUTION)                                                                          */
    /*        10            15            20            25                                                                    */
    /*    ------+-------------+-------------+------------+                                                                    */
    /*    |    95% Confidence Interval of the Mean       |                                                                    */
    /*    |                                              |                                                                    */
    /*    |                     16.4                  24 |                                                                    */
    /*  RF+                       |--------20----------| +RF                                                                  */
    /*    |                                              |                                                                    */
    /*    | 9.0                  16.6                    |                                                                    */
    /*  LF+  |---------13----------| 16.6                +LF                                                                  */
    /*    |                                              |                                                                    */
    /*    ------+-------------+-------------+------------+                                                                    */
    /*         10            15            20           25                                                                    */
    /*                              95% CI                                                                                    */
    /*                                                                                                                        */
    /*  Since sample are almost = 25/26 we can compare bins                                                                   */
    /*                                                                                                                        */
    /*         2   4   6   8    2   4   6   8  10                                                                             */
    /*       --+---+---+---+   -+---+---+---+---+------                                                                       */
    /*      | LEFT FIELD        RIGHT FIELD            |                                                                      */
    /*      |                                          |                                                                      */
    /*      |           PERCENT                PERCENT |                                                                      */
    /*      |                                          |BINS                                                                  */
    /*  BINS| **            2.0                    0.0 |39                                                                    */
    /*    33| ******        5.9 ****               3.9 |33                                                                    */
    /*    27| ************ 11.8 **                 2.0 |27                                                                    */
    /*    21| ************ 11.8 **********         9.8 |21                                                                    */
    /*    15| ************ 11.8 ******             5.9 |15                                                                    */
    /*     9| ****          3.9 ****************  15.7 | 9                                                                    */
    /*     3| ****          3.9 ************      11.8 | 3                                                                    */
    /*       --+---+---+---+   -+---+---+---+---+-------                                                                      */
    /*         2   4   6   8    2   4   6   8  10                                                                             */
    /*         HOME RUNS             HOME RUNS                                                                                */
    /*        LEFT FEILD            RIGHT FEILD                                                                               */
    /*                                                                                                                        */
    /*  PROCESS                                                                                                               */
    /*                                                                                                                        */
    /*  proc ttest data=sd1.have sides=L alpha=0.05;                                                                          */
    /*     class pos;                                                                                                         */
    /*     var homers;                                                                                                        */
    /*  run;                                                                                                                  */
    /*                                                                                                                        */
    /*  *Check T value;                                                                                                       */
    /*  data p_value;                                                                                                         */
    /*      t_value = -2.82;                                                                                                  */
    /*      df = 49;                                                                                                          */
    /*      p_value_one_tailed =                                                                                              */
    /*        1 - probt(abs(t_value), df);                                                                                    */
    /*  run;                                                                                                                  */
    /*                                                                                                                        */
    /*                     P_VALUE_                                                                                           */
    /*   T_VALUE    DF    ONE_TAILED                                                                                          */
    /*                                                                                                                        */
    /*    -2.82     49     .0034564                                                                                           */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */
