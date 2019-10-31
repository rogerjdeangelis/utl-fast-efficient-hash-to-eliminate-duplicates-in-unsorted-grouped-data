# utl-fast-efficient-hash-to-eliminate-duplicates-in-unsorted-grouped-data
Fast efficient has to eliminate duplicates in unsorted grouped data

    Eliminate duplicate test and res values within group

    github
    https://tinyurl.com/yyyvdmyg
    https://github.com/rogerjdeangelis/utl-fast-efficient-hash-to-eliminate-duplicates-in-unsorted-grouped-data

    SAS Forum
    https://communities.sas.com/t5/SAS-Programming/Multiple-records/m-p/600443

    Novinosrin
    https://communities.sas.com/t5/user/viewprofilepage/user-id/138205

    DonH  (add Don's suggestion)
    https://communities.sas.com/t5/user/viewprofilepage/user-id/13569

    *_                   _
    (_)_ __  _ __  _   _| |_
    | | '_ \| '_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    ;

    data have;
    input ID Visit$ Test$ res;
    cards4;
    1001 Screen Height 170
    1001 Screen Weight 82
    1001 Screen Height
    1001 Screen Weight 82
    1001 Day1 Height 170
    1001 Day1 Weight 89
    1001 Day1 Height 170
    1001 Day1 Weight
    1002 Screen Height 180
    1002 Screen Weight 95
    1002 Day1 Height 180
    1002 Day1 Weight 95
    ;;;;
    run;quit;

                                              |  RULE
                                              |  Eliminate duplicate test and res values within group
    Up to 40 obs WORK.HAVE total obs=10       |
                                              |
    Obs     ID     VISIT      TEST      RES   |
                                              |
      1    1001    Screen    Height     170   |  Keep

      2    1001    Screen    Weight      82   |
      3    1001    Screen    Height    1001   |
      4    1001    Day1      Height     170   |  Remove
      5    1001    Day1      Weight      89   |
      6    1001    Day1      Height     170   |  Remove
      7    1001    Day1      Weight    1002   |
                                              |
      8    1002    Screen    Weight      95   |

      9    1002    Day1      Height     180   |  Remove
     10    1002    Day1      Weight      95   |


    *            _               _
      ___  _   _| |_ _ __  _   _| |_
     / _ \| | | | __| '_ \| | | | __|
    | (_) | |_| | |_| |_) | |_| | |_
     \___/ \__,_|\__| .__/ \__,_|\__|
                    |_|
    ;

    WORK.WANT total obs=7

    bs     ID     VISIT      TEST      RES

    1     1001    Screen    Height     170
    2     1001    Screen    Weight      82
    3     1001    Screen    Height    1001
    4     1001    Day1      Weight      89
    5     1001    Day1      Weight    1002
    6     1002    Screen    Weight      95
    7     1002    Day1      Height     180


    *
     _ __  _ __ ___   ___ ___  ___ ___
    | '_ \| '__/ _ \ / __/ _ \/ __/ __|
    | |_) | | | (_) | (_|  __/\__ \__ \
    | .__/|_|  \___/ \___\___||___/___/
    |_|
    ;



    data want;
    if _n_=1 then do;
       dcl hash H () ;
       h.definekey  ("id","test","res") ;
       h.definedata ("id","test","res") ;
       h.definedone () ;
    end;
     do until(last.id);
      set have;
      by id;
      where res>.;
      if h.check()=0 then continue;
      h.add();
      output;
     end;
     h.clear();
    run;


    DonH comment (has been applied)
    A little note, if I may: Here you can call the ADD method unassigned (i.e. without coding rc=) because due
    to the subsetting IF logic, it is always successful - the method is called only when the key is not
    in the table, and so there's no risk of getting a method call error.


