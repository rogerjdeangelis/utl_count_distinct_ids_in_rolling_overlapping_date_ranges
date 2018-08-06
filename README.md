# utl_count_distinct_ids_in_rolling_overlapping_date_ranges
Count distinct ids in rolling overlapping date ranges.  Keywords: sas sql join merge big data analytics macros oracle teradata mysql sas communities stackoverflow statistics artificial inteligence AI Python R Java Javascript WPS Matlab SPSS Scala Perl C C# Excel MS Access JSON graphics maps NLP natural language processing machine learning igraph DOSUBL DOW loop stackoverflow SAS community.

    Count distinct ids in rolling overlapping date ranges

    This is a deceptively difficult problem?, nicely solved by PGStats.

    see github
    https://tinyurl.com/y9o8kt7z
    https://github.com/rogerjdeangelis/utl_count_distinct_ids_in_rolling_overlapping_date_ranges

    Same results in WPS and SAS after making a slight adjustment to the WPS code
    WPS does not support 'call missing(of pr[*])', when pr is a temporary array, yet.


    The filter to exculde dates between date1 and date2 and
    the by year requirement is not addressed?.

    You should be able to program those requirements by enhancing K Sharp's code.

    I tried to envision an easier solution but was unable?

    I find it easier to display the  numeric format of SAS dates.

    PGStats profile
    https://communities.sas.com/t5/user/viewprofilepage/user-id/462

    see
    https://tinyurl.com/ybpympkb
    https://communities.sas.com/t5/General-SAS-Programming/Count-number-of-observations-in-10-day-periods/m-p/478173



    INPUT
    =====

     WORK.HAVE total obs=12


                          |  RULES
                          |
     Obs     ID      DATE |  DATE  DISTNCT_ID
                          |
      1    10001    14610 | 14610      3    distinct IDs between 14610 and 14619 is 10001, 10002 and 1003
      2    10001    14611 | 14611      3    distinct IDs between 14611 and 14620 is
      3    10001    14612 | 14612      2    distinct IDs between 14612 and 14621 is 10001, 10002 (10003 is not)
                            14613      0    no distinct IDs between 14613 and 14619
      4    10002    14611 |
      5    10002    14612 |
      6    10002    14610 |
      7    10003    14611 |
      8    10004    14627 |
      9    10005    14628 |
     10    10006    14645 |
     11    10006    14646 |
     12    10006    14647 |


    PROCESS
    =======

     proc sort data=have out=work.have;
       by id date;
     run;quit;

     proc sql ;
     select min(date) - 9, max(date) into :beg trimmed, :end trimmed
     from have;
     quit;

     data want;

     array pr[&beg:&end] p&beg-p&end;
     array cnt[&beg:&end] _temporary_;

     call missing(of pr[*]);

     do until(last.id);
         set have end=done; by id;
         do i = date-9 to date;
             pr[i] = 1;
             end;
         end;
         do i = &beg to &end;
            if pr[i] then cnt[i] = sum(cnt[i], 1);
         end;
         if done then do;
           do date = &beg + 9 to &end - 9;
             distinctId = coalesce(cnt[date], 0);
             output;
           end;
         end;
     label date="10-day period start date";
     keep date distinctId;
     run;quit;
     proc print;
     run;quit;

    OUTPUT
    ======

     WORK.WANT total obs=29

        DATE    DISTINCTID

       14610         3
       14611         3
       14612         2
       14613         0
       14614         0
       14615         0
       14616         0
       14617         0
       14618         1
       14619         2
       14620         2
       14621         2
       14622         2
       14623         2
       14624         2
       14625         2
       14626         2
       14627         2
       14628         1
       14629         0
       14630         0
       14631         0
       14632         0
       14633         0
       14634         0
       14635         0
       14636         1
       14637         1
       14638         1

    *                _               _       _
     _ __ ___   __ _| | _____     __| | __ _| |_ __ _
    | '_ ` _ \ / _` | |/ / _ \   / _` |/ _` | __/ _` |
    | | | | | | (_| |   <  __/  | (_| | (_| | || (_| |
    |_| |_| |_|\__,_|_|\_\___|   \__,_|\__,_|\__\__,_|

    ;

    data have;
     input id date;
     informat date mmddyy8.;
    cards4;
    10001 01012000
    10001 01022000
    10001 01032000
    10002 01022000
    10002 01032000
    10002 01012000
    10003 01022000
    10004 01182000
    10005 01192000
    10006 02052000
    10006 02062000
    10006 02072000
    ;;;;
    run;quit;

    *          _       _   _
     ___  ___ | |_   _| |_(_) ___  _ __
    / __|/ _ \| | | | | __| |/ _ \| '_ \
    \__ \ (_) | | |_| | |_| | (_) | | | |
    |___/\___/|_|\__,_|\__|_|\___/|_| |_|

    ;


    %utl_submit_wps64('
     libname wrk sas7bdat "%sysfunc(pathname(work))";
     proc sort data=wrk.have out=work.have;
       by id date;
     run;quit;

     proc sql ;
     select min(date) - 9, max(date) into :beg trimmed, :end trimmed
     from have;
     quit;

     data want;

     array pr[&beg:&end] p&beg-p&end;
     array cnt[&beg:&end] _temporary_;

     call missing(of pr[*]);

     do until(last.id);
         set have end=done; by id;
         do i = date-9 to date;
             pr[i] = 1;
             end;
         end;
         do i = &beg to &end;
            if pr[i] then cnt[i] = sum(cnt[i], 1);
         end;
         if done then do;
           do date = &beg + 9 to &end - 9;
             distinctId = coalesce(cnt[date], 0);
             output;
           end;
         end;
     label date="10-day period start date";
     keep date distinctId;
     run;quit;
     proc print;
     run;quit;
    ');


