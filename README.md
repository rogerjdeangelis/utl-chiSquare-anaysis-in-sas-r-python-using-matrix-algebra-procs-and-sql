# utl-chiSquare-anaysis-in-sas-r-python-using-matrix-algebra-procs-and-sql
chiSquare anaysis in sas r python using matrix algebra procs packages and sql
    %let pgm=utl-chiSquare-anaysis-in-sas-r-python-using-matrix-algebra-procs-and-sql;

    %stop_submission; /* prevent accidental submission of all solutions at once */

    chiSquare anaysis in sas r python using matrix algebra procs packages and sql

    github
    http://tinyurl.com/3wk8rjyb
    https://github.com/rogerjdeangelis/utl-chiSquare-anaysis-in-sas-r-python-using-matrix-algebra-procs-and-sql

    Except for SQL, I found it difficult to sum over matrix elements in python. The
    python matrix code does not look like SAS or R. Note I am not concerned with performance
    because in memory solutions in R and Python limit the size of matrices.

    Even summing one column in a dataframe python is not like summing in any other language?

    Sum a column 1in a dataframe

       Python total   want[['chisq']].sum()  /* not like any other language not intuitive */

       R              sum(have$CNT)          /* all three are intuitive     */
       R              sum(have[,'CNT'])
       R              sum(have[,1]);

                                       rbeg rend  cbeg  cend
       SAS            summation(have,     1,   1,    1, nrows)  /* column 1 sum */

    Also I don't worry about repetative code in sql, the compiler probably recognizes
    previous exact formulas and doe not repeat the calculation.

    My goal is show how you can use code in r and python that is similar to sas

      1 r matrix algebra
      2 sas datastep matrix (similar to r matrix solution uses fcmp function)
      3 r chisq function
      4 r sql
      5 python sql


    https://online.stat.psu.edu/statprogram/reviews/statistical-concepts/chi-square-tests

    /*               _     _
     _ __  _ __ ___ | |__ | | ___ _ __ ___
    | `_ \| `__/ _ \| `_ \| |/ _ \ `_ ` _ \
    | |_) | | | (_) | |_) | |  __/ | | | | |
    | .__/|_|  \___/|_.__/|_|\___|_| |_| |_|
    |_|
    */

    /**************************************************************************************************************************/
    /*                                          |                                     |                                       */
    /*        INPUT                             |                                     |               OUTPUT                  */
    /*                                          |      PROC FREQ                      | Conclusion:                           */
    /*                                          |                                     | Education level depends on gender     */
    /*                                          |                                     |                                       */
    /*                                          |                                     | Counts decrease with years for Females*/
    /*                                          |                                     | but increase for males in raw counts  */
    /* Are gender and education level           |  ods output crosstabfreqs=          |                                       */
    /* dependent at a 5% level of significance? |          xtab(where=(_type_='11')); |                EXPECTED COUNTS        */
    /* In other words, given the data           |  ods output chisq=chisq;            | SEX    _12     _16     _18     _20    */
    /* collected above, is there a relationship |  proc freq data=sample;             |                                       */
    /* between the gender of an                 |   tables sex*yr / expected chisq;   | F   50.8861 49.8684 50.3772 49.8684   */
    /* individual and the level of              |   weight cnt;                       | M   49.1139 48.1316 48.6228 48.1316   */
    /* education that they have obtained?       |  run;quit;                          |                                       */
    /*                                          |                                     |                                       */
    /*       Years of Education                 |  proc transpose data=xtab           |                CHISQ                  */
    /*                                          |   (keep=sex yr expected) out=xpo ;  |                                       */
    /*        12  16 18 20  Sum                 |  by sex;                            | STATISTIC     DF    VALUE      PROB   */
    /*                                          |  id yr;                             |                                       */
    /* F      60  54 46 41  201                 |  run;quit;                          | Chi-Square     3   8.0061    0.0459   */
    /* M      40  44 53 57  194                 |                                     |                                       */
    /*                                          |  Since 8.006 > 7.815, we reject the |                                       */
    /* Total 100  98 99 98  395                 |  null and conclued                  | EXPLANATION                           */
    /*                                          |                                     |                                       */
    /* Counts decrease with years Females       |  Education level depends on gender  | The critical value of chiSq           */
    /* but increase for males in raw counts     |                                     | with 3 degrees of freedom is 7.815.   */
    /*                                          |                                     |                                       */
    /*                                          |                                     | null hypothesis and conclude that     */
    /*                                          |                                     | the education level depends           */
    /*                                          |                                     | on gender at 5% level of significance */.
    /*                                          |                                     |                                       */
    /*                                          |                                     | Note counts decrease with age         */
    /*                                          |                                     | for females while counts decrease     */
    /*                                          |                                     | with with age for males.              */
    /*                                          |                                     | The trend differently                 */
    /*                                          |                                     |                                       */
    /**************************************************************************************************************************/

    /*                   _
    (_)_ __  _ __  _   _| |_
    | | `_ \| `_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    */

    /*----                                                                   ----*/
    /*---- Two input data structures fat and skinny forms                    ----*/
    /*---- Skinny is used with SQL and SAS proc freq. Fat is used            ----*/
    /*---- with matrix algebra                                               ----*/
    /*----                                                                   ----*/

    options validvarname=upcase;
    libname sd1 "d:/sd1";
    data sd1.have;
    sex="F";yr=12;cnt=60;output; yr=16;cnt=54;output; yr=18;cnt=46;output; yr=20;cnt=41;output;
    sex="M";yr=12;cnt=40;output; yr=16;cnt=44;output; yr=18;cnt=53;output; yr=20;cnt=57;output;
    run;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*  Input in two datastrusctures                                                                                          */
    /*                                                                                                                        */
    /*  For proc freq                                                                                                         */
    /*                                                                                                                        */
    /*  Obs    SEX    YR    CNT                                                                                               */
    /*                                                                                                                        */
    /*   1      F     12     60                                                                                               */
    /*   2      F     16     54                                                                                               */
    /*   3      F     18     46                                                                                               */
    /*   4      F     20     41                                                                                               */
    /*   5      M     12     40                                                                                               */
    /*   6      M     16     44                                                                                               */
    /*   7      M     18     53                                                                                               */
    /*   8      M     20     57                                                                                               */
    /*                                                                                                                        */
    /*  Additional stucture for R, SAS matrix algebra solutions                                                               */
    /*                                                                                                                        */
    /*  array observed[2,4] _temporary_                                                                                       */
    /*          (                                                                                                             */
    /*           60 54 46 41                                                                                                  */
    /*           40 44 53 57                                                                                                  */
    /*          );                                                                                                            */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*
     _                          _        _              _            _
    / |  _ __   _ __ ___   __ _| |_ _ __(_)_  __   __ _| | __ _  ___| |__  _ __ __ _
    | | | `__| | `_ ` _ \ / _` | __| `__| \ \/ /  / _` | |/ _` |/ _ \ `_ \| `__/ _` |
    | | | |    | | | | | | (_| | |_| |  | |>  <  | (_| | | (_| |  __/ |_) | | | (_| |
    |_| |_|    |_| |_| |_|\__,_|\__|_|  |_/_/\_\  \__,_|_|\__, |\___|_.__/|_|  \__,_|
                                                          |___/

    */

    %utl_rbegin;
    parmcards4;
    observed <- matrix(c(60, 54, 46, 41, 40, 44, 53, 57), nrow = 2, ncol = 4, byrow = T)
    expected <- matrix(c(NA ,NA ,NA ,NA ,NA ,NA ,NA ,NA), nrow = 2, ncol = 4, byrow = T)

    coltots <- colSums(observed);
    rowtots <- rowSums(observed);

     chisq<-0;

     for (r in seq(1,nrow(observed),1)) {
       for (c in seq(1,ncol(observed),1)) {
           expected[r,c] <- rowtots[r]*coltots[c]/sum(observed);
           chisq         <- chisq + (expected[r,c] - observed[r,c])^2/expected[r,c];
       }
     }
    expected
    chisq

    sum(observed[1,1:nrows];

    critval<-qchisq(p = .05, df = 3, lower.tail = FALSE)

    if (chisq > critval) { cat("Obseved chisq > critical value at p=0.05 ",chisq,">",critval," rows depend on columns \n") }
    ;;;;
    %utl_rend;


    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*  Expected                                                                                                              */
    /*           [,1]     [,2]     [,3]     [,4]                                                                              */
    /*  [1,] 50.88608 49.86835 50.37722 49.86835                                                                              */
    /*  [2,] 49.11392 48.13165 48.62278 48.13165                                                                              */
    /*                                                                                                                        */
    /*  > chisq                                                                                                               */
    /*  [1] 8.006066                                                                                                          */
    /*                                                                                                                        */
    /* Obseved chisq > critical value 8.006066 > 7.814728 at p=0.05  rows depend on columns                                   */
    /*                                                                                                                        */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*___                        _       _            _                              _        _
    |___ \   ___  __ _ ___    __| | __ _| |_ __ _ ___| |_ ___ _ __   _ __ ___   __ _| |_ _ __(_)_  __
      __) | / __|/ _` / __|  / _` |/ _` | __/ _` / __| __/ _ \ `_ \ | `_ ` _ \ / _` | __| `__| \ \/ /
     / __/  \__ \ (_| \__ \ | (_| | (_| | || (_| \__ \ ||  __/ |_) || | | | | | (_| | |_| |  | |>  <
    |_____| |___/\__,_|___/  \__,_|\__,_|\__\__,_|___/\__\___| .__/ |_| |_| |_|\__,_|\__|_|  |_/_/\_\
                                                             |_|

    options cmplib=work.functions;

    proc fcmp outlib=work.functions.samples ;
    function summation(rc[*,*],rbeg,rend,cbeg,cend);
       total=0;
        do i= rbeg to rend;
          do j=cbeg to cend;
             total=total + rc[i,j];
          end;
        end;
    return(total);
    endsub;
    run;quit;

    data want;

      array observed[2,4] _temporary_
              (
               60 54 46 41
               40 44 53 57
              );

      array expected[2,4] _temporary_
              (
               4*.
               4*.
              );
       array rowsum[2] _temporary_ ( 2*. );
       array colsum[4] _temporary_ ( 4*. );

       nr = dim(observed,1);
       nc = dim(observed,2);

       grandtot = sum(of observed(*));

       do r=1 to nr;
         rowsum[r] = summation(observed,r,r,1,nc);
       end;

       do c=1 to nc;
         colsum[c] = summation(observed,1,nr,c,c);
       end;

       chisq=0;
       put 'Expected' /;
       do r=1 to nr;
          do c=1 to nc;
            expected[r,c]= (rowsum[r] * colsum[c])/grandtot;
            chisq = chisq + (observed[r,c] - expected[r,c])**2/expected[r,c];
            put expected[r,c]  5.2  "    " @;
          end;
          put;
       end;

       put / chisq =;

       call symputx("chiSq",chisq);

       stop;

    run;quit;

    %put &=chisq;

    %let critval=%sysfunc(cinv(.95, 3));
    %put &=critval;

    %if &chisq > &critval %then %do;
        %put Obseved chisq > critical value &chisq > &critval at p=0.05  rows depend on columns;
    %end;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* Expected                                                                                                               */
    /*                                                                                                                        */
    /* 50.89    49.87    50.38    49.87                                                                                       */
    /* 49.11    48.13    48.62    48.13                                                                                       */
    /*                                                                                                                        */
    /* CHISQ=8.0060662463                                                                                                     */
    /*                                                                                                                        */
    /* Obseved chisq > critical value 8.0060662463 > 7.81472790325118 at p=0.05  rows depend on columns                       (/
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*____               _     _              __                  _   _
    |___ /   _ __    ___| |__ (_)___  __ _   / _|_   _ _ __   ___| |_(_) ___  _ __
      |_ \  | `__|  / __| `_ \| / __|/ _` | | |_| | | | `_ \ / __| __| |/ _ \| `_ \
     ___) | | |    | (__| | | | \__ \ (_| | |  _| |_| | | | | (__| |_| | (_) | | | |
    |____/  |_|     \___|_| |_|_|___/\__, | |_|  \__,_|_| |_|\___|\__|_|\___/|_| |_|
                                        |_|
    */

    %utl_rbegin;
    parmcards4;
    observed <- matrix(c(60, 54, 46, 41, 40, 44, 53, 57), nrow = 2, ncol = 4, byrow = T)
    observed
    rownames(observed) <- c("Female", "Male")
    colnames(observed) <- c("12","16","18","20")
    X <- chisq.test(observed)
    X
    X$expected
    X$residuals
    X$stdres
    str(X)
    ;;;;
    %utl_rend;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* 	Pearson's Chi-squared test                                                                                            */
    /*                                                                                                                        */
    /* data:  observed                                                                                                        */
    /* X-squared = 8.0061, df = 3, p-value = 0.04589                                                                          */
    /*                                                                                                                        */
    /* > X$expected;                                                                                                          */
    /*              12       16       18       20                                                                             */
    /* Female 50.88608 49.86835 50.37722 49.86835                                                                             */
    /* Male   49.11392 48.13165 48.62278 48.13165                                                                             */
    /*                                                                                                                        */
    /*                                                                                                                        */
    /* Unlike regression the greater the residual the more likely yr depends on gender                                        */
    /*                                                                                                                        */
    /* > X$residuals                                                                                                          */
    /*               12         16         18        20                                                                       */
    /* Female  1.277632  0.5850737 -0.6167098 -1.255829                                                                       */
    /* Male   -1.300478 -0.5955356  0.6277374  1.278285                                                                       */
    /*                                                                                                                        */
    /* > X$stdres  (standardized residuals)                                                                                   */
    /*               12         16        18        20                                                                        */
    /* Female  2.109556  0.9627833 -1.016556 -2.066562                                                                        */
    /* Male   -2.109556 -0.9627833  1.016556  2.066562                                                                        */
    /*                                                                                                                        */
    /*                                                                                                                        */
    /* AVAILABLE DATA ( LIKE ODS TRACE)                                                                                       */
    /*                                                                                                                        */
    /* List of 9                                                                                                              */
    /*  $ statistic: Named num 8.01                                                                                           */
    /*   ..- attr(*, "names")= chr "X-squared"                                                                                */
    /*  $ parameter: Named int 3                                                                                              */
    /*   ..- attr(*, "names")= chr "df"                                                                                       */
    /*  $ p.value  : num 0.0459                                                                                               */
    /*  $ method   : chr "Pearson's Chi-squared test"                                                                         */
    /*  $ data.name: chr "observed"                                                                                           */
    /*  $ observed : num [1:2, 1:4] 60 40 54 44 46 53 41 57                                                                   */
    /*   ..- attr(*, "dimnames")=List of 2                                                                                    */
    /*   .. ..$ : chr [1:2] "Female" "Male"                                                                                   */
    /*   .. ..$ : chr [1:4] "12" "16" "18" "20"                                                                               */
    /*  $ expected : num [1:2, 1:4] 50.9 49.1 49.9 48.1 50.4 ...                                                              */
    /*   ..- attr(*, "dimnames")=List of 2                                                                                    */
    /*   .. ..$ : chr [1:2] "Female" "Male"                                                                                   */
    /*   .. ..$ : chr [1:4] "12" "16" "18" "20"                                                                               */
    /*  $ residuals: num [1:2, 1:4] 1.278 -1.3 0.585 -0.596 -0.617 ...                                                        */
    /*   ..- attr(*, "dimnames")=List of 2                                                                                    */
    /*   .. ..$ : chr [1:2] "Female" "Male"                                                                                   */
    /*   .. ..$ : chr [1:4] "12" "16" "18" "20"                                                                               */
    /*  $ stdres   : num [1:2, 1:4] 2.11 -2.11 0.963 -0.963 -1.017 ...                                                        */
    /*   ..- attr(*, "dimnames")=List of 2                                                                                    */
    /*   .. ..$ : chr [1:2] "Female" "Male"                                                                                   */
    /*   .. ..$ : chr [1:4] "12" "16" "18" "20"                                                                               */
    /*  - attr(*, "class")= chr "htest"                                                                                       */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*  _                      _
    | || |    _ __   ___  __ _| |
    | || |_  | `__| / __|/ _` | |
    |__   _| | |    \__ \ (_| | |
       |_|   |_|    |___/\__, |_|
                            |_|
    */

    %utl_rbegin;
    parmcards4;
    library(sqldf);
    library(haven);
    have<-read_sas("d:/sd1/have.sas7bdat")
    have;
    havchi<-sqldf("
      select
        *
      from
        (select
          l.sex
         ,l.yr
         ,l.cnt
         ,c.rowtot
         ,r.coltot
         ,c.rowtot*r.coltot / (select sum(cnt) from have) as expected
         ,power((cnt - c.rowtot*r.coltot / (select sum(cnt) from have)),2) /
             (c.rowtot*r.coltot / (select sum(cnt) from have)) as chisq
        from
           have as l
            left join (select sex ,sum(cnt)  as rowtot from have group by sex) as c on l.sex = c.sex
            left join (select yr  ,sum(cnt)  as coltot from have group by yr)  as r on l.yr  = r.yr
        )
    ")
    havchi
    chisq<-sum(havchi$chisq)
    critval<-qchisq(p = .05, df = 3, lower.tail = FALSE)

    if (chisq > critval) { cat("Obseved chisq > critical value at p=0.05 ",chisq,">",critval," rows depend on columns \n") }
    ;;;;
    %utl_rend;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*  > havchi                                                                                                              */
    /*    SEX YR CNT ROWTOT COLTOT EXPECTED     CHISQ                                                                         */
    /*                                                                                                                        */
    /*  1   F 12  60    201    100 50.88608 1.6323446                                                                         */
    /*  2   F 16  54    201     98 49.86835 0.3423112                                                                         */
    /*  3   F 18  46    201     99 50.37722 0.3803309                                                                         */
    /*  4   F 20  41    201     98 49.86835 1.5771066                                                                         */
    /*  5   M 12  40    194    100 49.11392 1.6912436                                                                         */
    /*  6   M 16  44    194     98 48.13165 0.3546626                                                                         */
    /*  7   M 18  53    194     99 48.62278 0.3940542                                                                         */
    /*  8   M 20  57    194     98 48.13165 1.6340125                                                                         */
    /*                                                                                                                        */
    /*  Obseved chisq > critical value at p=0.05  8.006066 > 7.814728  rows depend on columns                                 */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*___                _   _                             _
    | ___|   _ __  _   _| |_| |__   ___  _ __    ___  __ _| |
    |___ \  | `_ \| | | | __| `_ \ / _ \| `_ \  / __|/ _` | |
     ___) | | |_) | |_| | |_| | | | (_) | | | | \__ \ (_| | |
    |____/  | .__/ \__, |\__|_| |_|\___/|_| |_| |___/\__, |_|
            |_|    |___/                                |_|
    */

    options validvarname=upcase;
    libname sd1 "d:/sd1";
    data sd1.have;
    sex="F";yr=12;cnt=60;output; yr=16;cnt=54;output; yr=18;cnt=46;output; yr=20;cnt=41;output;
    sex="M";yr=12;cnt=40;output; yr=16;cnt=44;output; yr=18;cnt=53;output; yr=20;cnt=57;output;
    run;quit;

    %utl_pybegin;
    parmcards4;
    import scipy.stats as stats
    import pyreadstat as ps
    from os import path
    import pandas as pd
    import numpy as np
    from pandasql import sqldf
    mysql = lambda q: sqldf(q, globals())
    from pandasql import PandaSQL
    pdsql = PandaSQL(persist=True)
    sqlite3conn = next(pdsql.conn.gen).connection.connection
    sqlite3conn.enable_load_extension(True)
    sqlite3conn.load_extension('c:/temp/libsqlitefunctions.dll')
    mysql = lambda q: sqldf(q, globals())
    have, meta = ps.read_sas7bdat('d:/sd1/have.sas7bdat')
    want = pdsql('''
      select
        *
      from
        (select
          l.sex
         ,l.yr
         ,l.cnt
         ,c.rowtot
         ,r.coltot
         ,c.rowtot*r.coltot / (select sum(cnt) from have) as expected
         ,power((cnt - c.rowtot*r.coltot / (select sum(cnt) from have)),2) /
             (c.rowtot*r.coltot / (select sum(cnt) from have)) as chisq
        from
           have as l
            left join (select sex ,sum(cnt)  as rowtot from have group by sex) as c on l.sex = c.sex
            left join (select yr  ,sum(cnt)  as coltot from have group by yr)  as r on l.yr  = r.yr
        )
    ''')

    chisq = want[['chisq']].sum()
    print(want)
    print(chisq)
    critval=stats.chi2.ppf(0.95, 3)
    print("critical value ",critval)
    print("Obseved chisq > critical value at p=0.05 ",chisq,">",critval," rows depend on columns")
    ;;;;
    %utl_pyend;


    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*   SEX    YR   CNT  ROWTOT  COLTOT   EXPECTED     CHISQ                                                                 */
    /*                                                                                                                        */
    /* 0   F  12.0  60.0   201.0   100.0  50.886076  1.632345                                                                 */
    /* 1   F  16.0  54.0   201.0    98.0  49.868354  0.342311                                                                 */
    /* 2   F  18.0  46.0   201.0    99.0  50.377215  0.380331                                                                 */
    /* 3   F  20.0  41.0   201.0    98.0  49.868354  1.577107                                                                 */
    /* 4   M  12.0  40.0   194.0   100.0  49.113924  1.691244                                                                 */
    /* 5   M  16.0  44.0   194.0    98.0  48.131646  0.354663                                                                 */
    /* 6   M  18.0  53.0   194.0    99.0  48.622785  0.394054                                                                 */
    /* 7   M  20.0  57.0   194.0    98.0  48.131646  1.634012                                                                 */
    /*                                                                                                                        */
    /* chisq    8.006066                                                                                                      */
    /*                                                                                                                        */
    /* I had an issue formatting python output. I think you can see                                                           */
    /*                                                                                                                        */
    /* critical value  7.814727903251179  (chi square p=0.05 and 3 df)                                                        */
    /*                                                                                                                        */
    /* Obseved chisq > critical value at p=0.05  chisq    8.006066                                                            */
    /*       dtype: float64 > 7.814727903251179  rows depend on columns                                                       */
    /*                                                                                                                        */
    /* Note the compiler/ interpreter should only compute repeating code once (code may be faster than you think)             */
    /*                                                                                                                        */
    /*    c.rowtot*r.coltot            (appears three times)                                                                  */
    /*    (select sum(cnt) from have)  (appears twice)                                                                        */
    /*                                                                                                                        */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */
