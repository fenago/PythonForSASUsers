<img align="right" src="./logo-small.png">

# Lab : Date, Time, and Timedelta Objects

#### Pre-reqs:
- Google Chrome (Recommended)

#### Lab Environment
Notebooks are ready to run. All packages have been installed. There is no requirement for any setup.

**Note:** Elev8ed Notebooks (powered by Jupyter) will be accessible at the port given to you by your instructor. Password for jupyterLab : `1234`

All Notebooks are present in `work/PythonForSASUsers` folder. To copy and paste: use **Control-C** and to paste inside of a terminal, use **Control-V**

You can access jupyter lab at `<host-ip>:<port>/lab/workspaces/lab9_Objects`


##### Run Notebook
Click notebook `Chapter 08 -- Date, Time, and  Timedelta Objects.ipynb` in jupterLab UI and run jupyter notebook.

## Topics Covered:

String Literal Mapped to datetime timestamp

date objects

strftime() and strptime() methods

dateutil.parser

time objects

timedelta objects

Resources

Chapter 3, Data Types and Formatting briefly introduces Python date and datetimes.

This chapter covers Python Date, Time, and Datetime objects and details for handling these objects as well as mapping strings to datetime values and the reverse operation. By understanding the lower-level behaviors of Date and Time objects, you will be able to grasp Chapter 9, Pandas Time Series Capabilities and Datetime Handling.

Many time series have fixed interval or frequency, for example, home sales on a monthly basis or web log record processed every 15 seconds. Time series may also have irregular frequencies, in which case you may need to standardize, reshape, or change frequencies.

## Definitions

- Timestamps are instances in time

- Periods such as the current month of current year

- Intervals indicated by start and end timestamps

Recall, you can always return an object's type with the type method:

type()
In Python the datetime class be broken down into 5 categories:

The first 4 will be examined briefly.

![](.\images_8\.jpg)

```
In [1]:
from datetime import date, time, datetime, timedelta
import numpy as np
import pandas as pd
from pandas import Series, DataFrame, Index

## String Literal Mapped to datetime timestamp

A timestamp is time value that represents a count of the number of seconds from the start of an epoch. This is similiar to SAS datetime values that represent an off-set from an epoch beginning at midnight.

```
In [2]:
pdt = pd.Timestamp('2016-10-24')
```
In [3]:
type(pdt)
```

Out[3]:
pandas.tslib.Timestamp

## date objects

The syntax for constructing a date object is:

date(year = yyyy, month = mm, day = dd)

Where: yyyy is an integer ranging from 1 to 9999 by default
       mm is an integer ranging from 1 to 12 inclusive
       dd is an integer ranging from 1 to the number of days in the month of the year

The details for the date object are found here

Construct an arbitrary date

```
In [4]:
ind_day = date(1776, 7, 4)
print(ind_day)
print(type(ind_day))
1776-07-04
<class 'datetime.date'>
Return today's date. The SAS analog is the today() function.

```
In [5]:
date.today()
```

Out[5]:
datetime.date(2016, 11, 13)
The .year, .month, and .day attribute for datetime objects returns year, month, and day respectively. These attributes return integers.

SAS has the analog functions, year(), month(), day(), used to return the respective portions of a SAS datetime value. These functions return numeric values.

```
In [6]:
print('Year:', date.today().year)
print('Month:', date.today().month) 
print('Day:', date.today().day)
Year: 2016
Month: 11
Day: 13
The analog SAS program for the examples in cells #3 to #6 is below.

    /******************************************************/
    /* c08_datetime_functions1.sas                        */
    /******************************************************/
    37       data _null_;
    38       
    39       today = today();
    40       ind_day = '04Jul1776'd;
    41       
    42       today_f = put(today,mmddyy10.);
    43       put 'unformatted, today is: ' today /
    44           'formatted with mmddyy10. , today is: ' today_f;
    45       
    46       m = month(today);
    47       d = day(today);
    48       y = year(today);
    49       
    50       put 'Month returns: ' m
    51           ' Day returns: ' d
    52           ' Year returns ' y;

    unformatted, today is: 20748
    formatted with mmddyy10. , today is: 10/21/2016
    Month returns: 10  Day returns: 21  Year returns 2016
The .year, .month, and .day attributes also work with arbitrary dates as well.

```
In [7]:
print('Year:', ind_day.year, 'Month:', ind_day.month, 'and day:', ind_day.day)
Year: 1776 Month: 7 and day: 4
The weekday() method returns an integer representing day of the week where Monday is 0 and Sunday is 6. The SAS analog is the WEEKDAY() function that returns numeric where 1 is Sunday and Saturday is 7.

```
In [8]:
print(date.weekday(ind_day))
3

## strftime() and strptime() methods
Like SAS, when working with datetime objects, formatting is needed to render dates into familiar date and time designations. In cell # 9 below, we render the constituent parts of the ind_day object created in cell #2 above. We also need formatting when creating datetime objects from string representations of datetime illustrated below.

The strftime() method (string formatter for time) is used to create string repersentations of Python datetime objects.

The strptime() method (string parser for time) is used to create datetime objects from string representations of datetime.

The directives for formatting both are found here.

SAS formats are analogs to the strftime() method. SAS informats are analogs to the strptime() method.

**strftime examples**

```
In [9]:
print('Day:', ind_day.strftime('%A'))
print('The convential method to display the date is:', ind_day.strftime('%c'))
print('Another way to display the date is:', ind_day.strftime('%A %B %d, %Y'))
Day: Thursday
The convential method to display the date is: Thu Jul  4 00:00:00 1776
Another way to display the date is: Thursday July 04, 1776
The SAS example below illustrate these commonly used SAS formats:

date11.

ddmmyy10.

weekdate17.

yymmdd10.

The strftime() method apply format directives to datetime objects. As illustrated above, these directives can be combined to form the desired output. This includes any arbitrary characters such as comma, slash, or white-space needed to form the output.

```
In [10]:
last_day = date(2016, 12, 31)
print('Last day is:', last_day.strftime("%d-%b-%Y"))        # date11. format
print('Last day is:', last_day.strftime("%d/%m/%Y"))        # ddmmyy10. format
print('Last day is:', last_day.strftime("%a, %b %d, %Y"))   # weekdate17. format
print('Last day is:', last_day.strftime("%Y-%m-%d"))        # yymmdd10. format
Last day is: 31-Dec-2016
Last day is: 31/12/2016
Last day is: Sat, Dec 31, 2016
Last day is: 2016-12-31
The SAS program below illustrates formats to map datetime values into string representation similiar to the strftime() method.

    /******************************************************/
    /* c08_lastday_formats.sas                            */
    /******************************************************/
    4         data _null_;
    5         last_day = '31Dec16'd;
    6         
    7         ld_f1 = put(last_day, date11.);
    8         ld_f2 = put(last_day, ddmmyy10.);
    9         ld_f3 = put(last_day, weekdate17.);
    10        ld_f4 = put(last_day, yymmdd10.);
    11        
    12        put 'Last day is: ' ld_f1;
    13        put 'Last day is: ' ld_f2;
    14        put 'Last day is: ' ld_f3;
    15        put 'Last day is: ' ld_f4;

    Last day is: 31-DEC-2016
    Last day is: 31/12/2016
    Last day is: Sat, Dec 31, 2016
    Last day is: 2016-12-31
The strptime() method is used to create or parse a datetime object from a string representation of datetime corresponding to the format string supplied. See the example below in cell #9.

```
In [11]:
str_date = "01/01/2016"
first_date = datetime.strptime(str_date, "%d/%m/%Y" )
print(first_date)
print('type for str_date is:', type(str_date))
print('type for first_date is:', type(first_date))
2016-01-01 00:00:00
type for str_date is: <class 'str'>
type for first_date is: <class 'datetime.datetime'>
The SAS program below illustrates informats to map string representations of datetime into a SAS datetime value similiar to the strptime() method.

    /******************************************************/
    /* c08_informat_string_to_datetime.sas                */
    /******************************************************/
    45        data _null_;
    46        
    47        str_date = "01/01/2016";
    48        
    49        first_date = input(str_date, ddmmyy10.);
    50        
    51        put first_date ddmmyy10.;

    01/01/2016

## dateutil.parser

It can be tedious to constantly write the directives used to control the strptime() method. A useful alternative is to use the third-party dateutil.parser. It can parse nearly any datetime string literals into datetime objects.

```
In [12]:
from dateutil.parser import parse
```
In [13]:
strt_yr = parse('2016/01/01')
strt_yr
```

Out[13]:
datetime.datetime(2016, 1, 1, 0, 0)

**Basic date aritmetic illustrating the replace() method.**

```
In [14]:
d = date(2015, 12, 31)
if d == date(2015, 12, 31):
    d2 = d.replace(day=25) == date(2015, 12, 25)
print(d)
print(d2)
2015-12-31
True
```
In [15]:
today = date.today()

birth_day = date(today.year, 1, 24)

if birth_day < today:
    birth_day = birth_day.replace(year=today.year + 1)
print('Next birthday is:', birth_day)

days_until = abs(birth_day - today)
print(days_until)
print(type(days_until))
Next birthday is: 2017-01-24
72 days, 0:00:00
<class 'datetime.timedelta'>
The example above in cell #11 is another illustration of date arithmetic. The if condition determine whether the birthday has occured this year (in this case, True) and if True, uses the date.replace() method to increment to the next year. The syntax:

days_until = abs(birth_day - today)

returns a timedelta. As the name suggests, it is a duration period. Additional examples of working with timedelta objects is below.

The analog SAS program for counting the number of days until the next birthday is below.

In both the Python and SAS examples had we added 365 days in the conditional branch of the program the results would have been 94 days since 2016 is a leap year. This is a reminder that short-cuts like adding 365.25 to increment to the next year or adding 24 hours to increment to the following day is fraught with unintended consequences. That is why these functions and operators are available.

    /******************************************************/
    /* c08_birthdate_calculation.sas                      */
    /******************************************************/
    35       data _null_;
    36       
    37       today = today();
    38       
    39       birth_day = '24Jan16'd;
    40       
    41       if birth_day < today then do;
    42          next_birth_day = intnx('year', birth_day, 1, 'sameday');
    43          f_b_day = put(next_birth_day, mmddyy10.);
    44          put 'Next birthday is: ' f_b_day;
    45       end;
    46       
    47       days_until = abs(next_birth_day - today);
    48       put days_until ' days';

    Next birthday is: 01/24/2017
    95  days

## time objects

The time object behaves similiar to the date object with respect to displaying and mapping string representations of time into time objects. The SAS analogs are similar as well.

The syntax for constructing a time object is:

time(hour=hh, minute=mm, second=0ss, microsecond=ms)

Where hh is 0 <= hour < 24
      mm is 0 <= minute < 60
      ss is 0 <= second < 60
      ms is 0 <= microsecond < 1000000

The time object has two other parameters, tzinfo and fold outside the scope of these examples. The details for the time object are found here .

```
In [16]:
go_time = time(12, 34, 56)
print(go_time)
type(go_time)
12:34:56
```

Out[16]:
datetime.time
Get the current time of day. The SAS analog is the time() function.

```
In [17]:
datetime.now()
```

Out[17]:
datetime.datetime(2016, 11, 13, 16, 9, 29, 148013)
```
In [18]:
print(datetime.now().hour)
print(datetime.now().minute) 
print(datetime.now().second)
16
9
29
The .hour, .minute, and .second attribute for datetime objects returns hour, minutes, and seconds respectively. These attributes return integers.

SAS has the analog functions, hour(), minute(), second(), used to return the respective portions of a SAS datetime value. These functions return numeric values.

    /******************************************************/
    /* c08_datetime_functions2.sas                        */
    /******************************************************/
    21       data _null_;
    22       
    23       now = time();
    24       
    25       go_time = "12:34:45"t;
    26       
    27       now_f = put(now, time8.);
    28       put 'unformatted, now is ' now /
    29           'formatted with time8., now is: ' now_f;
    30       
    31       h = hour(now);
    32       m  = minute(now);
    33       s = second(now);
    34       
    35       put 'hour returns: ' h
    36           ' minute returns: ' m
    37           ' second returns: ' s;

    unformatted, now is 57327.064
    formatted with time8., now is: 15:55:27
    hour returns: 15  minute returns: 55  second returns: 27.063999891
The .hour, .minute, and .second attribute also work with arbitrary times.

```
In [19]:
print('Hour:', go_time.hour, 'Minutes:', go_time.minute, 'and seconds:', go_time.second)
Hour: 12 Minutes: 34 and seconds: 56

## timedelta objects

As the name suggests, a timedelta object represents the difference two date or times. In other words, a duration.

The details for the timedelta object are found here

The SAS analog are the various date interval functions such as:

INTNX

INTGET

INTFIT

and others that are similiar.

The results of an operation with a timedelta object returns a datetime object. The datetime method now() returns today's date similiar to the SAS function TODAY().

```
In [20]:
today = date.today()
moment = datetime.now().time()
now = datetime.combine(today, moment)
```
In [21]:
print(today, moment, now)
2016-11-13 16:09:29.487036 2016-11-13 16:09:29.487036
What is 1000 days from now

```
In [22]:
today+timedelta(days=1000)
```

Out[22]:
datetime.date(2019, 8, 10)
```
In [23]:
future = date(2020, 1, 1)
days_until = future - today
days_until
```

Out[23]:
datetime.timedelta(1144)
The SAS equivalent for timedelta object examples is below.

    /******************************************************/
    /* c08_timedelta_analog_example.sas                   */
    /******************************************************/
    44       data _null_;
    45       
    46       today = today();
    47       t_f = put(today,mmddyy10.);
    48       put 'Today is: ' t_f;
    49       
    50       yesterday = intnx('day', today, -1);
    51       y_f = put(yesterday,mmddyy10.);
    52       put 'Yesterday was: ' y_f;
    53       
    54       thousand_days = intnx('day', today, 1000, 'e');
    55       td_f = put(thousand_days, mmddyy10.);
    56       put '1,000 days from now is: ' td_f;
    57       
    58       future = '01Jan2020'd;
    59       days_until = future - today;
    60       
    61       put days_until=;

    Today is: 10/24/2016
    Yesterday was: 10/23/2016
   1,000 days from now is: 07/21/2019
   days_until=1164

## Resources

Chapter 10, Time Series, "Python for Data Analysis, by Wes McKinney, located here

strftime and strptime behavior

About Date and Time Intervals SAS 9.2 Language Reference: Concepts, Second Edition

Land Surface Hydrology Group Computing Seminar, by Joe Hamman, University of Washington, located here .

