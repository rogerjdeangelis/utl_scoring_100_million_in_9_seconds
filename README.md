# utl_scoring_100_million_in_9_seconds
Data analytics. Macine learning. Scoring 100 million records in 9 seconds not 2-3 hrs

    ```  Scoring 100 million records in 8.5 seconds not 2-3 hours  ```
    ```    ```
    ```  Interesting post in R stackoverflow. User wanted to use hadoop  ```
    ```    ```
    ```  SAS scored it in 8.5 seconds  ```
    ```    ```
    ```  "but I would still like to score it in about 2-3 hours"  ```
    ```    ```
    ```  SAS scored it 8.5 seconds on my $600 dell T7400,  ```
    ```  If I have time I will run it on a large EG server.  ```
    ```    ```
    ```  http://tinyurl.com/ppkuv74  ```
    ```    ```
    ```  As a side note there is no need to materialize the 8 output datasets.  ```
    ```  Just use a view. Sometimes it is nice to have the 8 pieces.  ```
    ```  Note the colon.  ```
    ```    ```
    ```  data alldata/view=alldata;  ```
    ```    set keyscore: ;  ```
    ```  run;quit;  ```
    ```    ```
    ```  proc datasets library=spde;  ```
    ```  delete littledata_spde;  ```
    ```  run;quit;  ```
    ```    ```
    ```  libname spde spde 'd:/tmp'  ```
    ```    datapath=("f:/wrk/spde_f" "e:/wrk/spde_e" "g:/wrk/spde_g")  ```
    ```    partsize=4g;  ```
    ```  ;  ```
    ```    ```
    ```  data spde.littledata_spde (compress=char drop=idx);  ```
    ```    retain primary_key;  ```
    ```    array num[20] n1-n20;  ```
    ```    array chr[20] $4 c1-c20;  ```
    ```    do primary_key=1 to 80000000;  ```
    ```      do idx=31 to 50;  ```
    ```        num[idx-30]=uniform(-1);  ```
    ```        chr[idx-30]=repeat(byte(idx),40);  ```
    ```      end;  ```
    ```      output;  ```
    ```    end;  ```
    ```  run;quit;  ```
    ```    ```
    ```    ```
    ```    ```
    ```  %let _s=%sysfunc(compbl(C:\Progra~1\SASHome\SASFoundation\9.4\sas.exe -sysin c:\nul -nosplash -sasautos c:\oto -autoexec c:\oto\Tut_Oto.sas));  ```
    ```    ```
    ```  * score it;  ```
    ```    ```
    ```    ```
    ```  data _null_;file "c:\oto\utl_scoreit.sas" lrecl=512;input;put _infile_;putlog _infile_;  ```
    ```  cards4;  ```
    ```  %macro utl_scoreit(beg=1,end=10000000);  ```
    ```    ```
    ```    libname spde spde 'd:/tmp'  ```
    ```    datapath=("f:/wrk/spde_f" "e:/wrk/spde_e" "g:/wrk/spde_g")  ```
    ```    partsize=4g;  ```
    ```    ```
    ```    libname out "G:/wrk";  ```
    ```    ```
    ```    data keyscore&beg;  ```
    ```    ```
    ```       set spde.littledata_spde&beg(firstobs=&beg obs=&end  ```
    ```          keep=  ```
    ```             primary_key  ```
    ```             n1  ```
    ```             n12  ```
    ```             n3  ```
    ```             n14  ```
    ```             n5  ```
    ```             n16  ```
    ```             n7  ```
    ```             n18  ```
    ```             n9  ```
    ```             n10  ```
    ```             c18  ```
    ```             c19  ```
    ```             c12);  ```
    ```      score= (.1*n1   +  ```
    ```              .1*n12  +  ```
    ```              .1*n3   +  ```
    ```              .1*n14  +  ```
    ```              .1*n5   +  ```
    ```              .1*n16  +  ```
    ```              .1*n7   +  ```
    ```              .1*n18  +  ```
    ```              .1*n9   +  ```
    ```              .1*n10  +  ```
    ```               (c18='0000')  +  ```
    ```               (c19='0000')  +  ```
    ```               (c12='0000'))/3  ;  ```
    ```      keep primary_key score;  ```
    ```    run;  ```
    ```    ```
    ```  %mend utl_scoreit;  ```
    ```  ;;;;  ```
    ```  run;quit;  ```
    ```    ```
    ```  %utl_scoreit;  ```
    ```    ```
    ```    ```
    ```  %let tym=%sysfunc(time());  ```
    ```  systask kill sys101 sys102 sys103 sys104  sys105 sys106 sys107 sys108;  ```
    ```  systask command "&_s -termstmt %nrstr(%utl_scoreit(beg=1,end=10000000);) -log G:\wrk\sys101.log" taskname=sys101;  ```
    ```  systask command "&_s -termstmt %nrstr(%utl_scoreit(beg=10000001,end=20000000);) -log G:\wrk\sys102.log" taskname=sys102 ;  ```
    ```  systask command "&_s -termstmt %nrstr(%utl_scoreit(beg=20000001,end=30000000);) -log G:\wrk\sys103.log" taskname=sys103 ;  ```
    ```  systask command "&_s -termstmt %nrstr(%utl_scoreit(beg=30000001,end=40000000);) -log G:\wrk\sys104.log" taskname=sys104 ;  ```
    ```  systask command "&_s -termstmt %nrstr(%utl_scoreit(beg=40000001,end=50000000);) -log G:\wrk\sys105.log" taskname=sys105 ;  ```
    ```  systask command "&_s -termstmt %nrstr(%utl_scoreit(beg=50000001,end=60000000);) -log G:\wrk\sys106.log" taskname=sys106 ;  ```
    ```  systask command "&_s -termstmt %nrstr(%utl_scoreit(beg=60000001,end=70000000);) -log G:\wrk\sys107.log" taskname=sys107 ;  ```
    ```  systask command "&_s -termstmt %nrstr(%utl_scoreit(beg=70000001,end=80000000);) -log G:\wrk\sys108.log" taskname=sys108 ;  ```
    ```  waitfor _all_ sys101 sys102 sys103 sys104  sys105 sys106 sys107 sys108;  ```
    ```  systask list;  ```
    ```  %put %sysevalf( %sysfunc(time()) - &tym);  ```
    ```    ```
    ```  8.56500005719863  ```
    ```    ```
    ```    ```
