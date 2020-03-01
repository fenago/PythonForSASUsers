<img align="right" src="./logo-small.png">

# Lab : Pandas, Part 1

#### Pre-reqs:
- Google Chrome (Recommended)

#### Lab Environment
Notebooks are ready to run. All packages have been installed. There is no requirement for any setup.

**Note:** Elev8ed Notebooks (powered by Jupyter) will be accessible at the port given to you by your instructor. Password for jupyterLab : `1234`

All Notebooks are present in `work/PythonForSASUsers` folder. To copy and paste: use **Control-C** and to paste inside of a terminal, use **Control-V**

You can access jupyter lab at `<host-ip>:<port>/lab/workspaces/lab5_Pandas`

##### Run Notebook
Click notebook `Chapter 04 -- Pandas, Part 1.ipynb` in jupterLab UI and run jupyter notebook.


## Topics covered:
- Importing Packages
- Series
- DataFrames
- Read .csv files
- Inspection
- Handling Missing Data
- Missing Data Detection
- Missing Value Replacement
- Resources

This chapter introduces the Pandas library (or package). panda is a package built using NumPy (pronounced 'numb pie').

Up until this point, the examples seen in this notebook utilize Python's built-in types and functions. NumPy has its ndarray object for array arithmetic. NumPy is a package built to support scientific computing in Python. We will illustrate a few useful NumPy objects as a way of illustrating pandas

panda was developed to support data analysis with added flexibility than offered by the ndarray object in NumPy. For data analysis tasks we often need to group dis-similar data types together. An examples are categorical data using strings, frequencies and counts using ints and floats for continuous values. In addition, we would like to be able to attach labels to columns, pivot data, and so on.

We begin by introducing the Series object as a component of the DataFrame objects. A Series can be thought of as an indexed, one-dimensional array, similar to a column of values. DataFrames can be thought of as a two-dimensional array indexed by both rows and columns. A good analogy is an Excel cell addressable by row and column location.

In other words, a DataFrame looks a great deal like a SAS data set (or relational table). The table below compares panda components to those found in SAS.

![](./images_4/1.jpg)

## Panda Pre-requisites

panda have three main data structures:

1. Series
2. DataFrame
3. Index

Indexes are covered in detail in Chapter 6, Understanding Indexes.

## Importing Packages

To begin utilizing panda objects, or other objects, begin by importing libraies by name into our namespace. To avoid having to re-typing package name repeatedly, use the standard aliases of np for NumPy and pd for panda.

```
In [1]:
import numpy as np
import pandas as pd
from numpy.random import randn
from pandas import Series, DataFrame, Index
from IPython.display import Image
```

## Series

A Series can be thought of as a one-dimensional array with labels. This structure includes an index of labels used as keys to locate values. Data in a Series can be any data type. panda data types are covered in detail here . In the SAS examples, we use Data Step ARRAYs as an analog to the Series.

Start by creating a Series of random values.

```
In [2]:
s1 = Series(randn(10))
print(s1.head(5))
0    1.470961
1    0.724744
2   -1.601498
3    0.201619
4   -1.106859
dtype: float64
Notice the index start position begins with 0. Most SAS automatic variables like _n_ use 1 as the index start position. Iteration of the SAS DO loop 0 to 9 in conjunction with an ARRAY produces an array subscript out of range error.

In the SAS example below the DO loop is used to iterate over the array elements locating the target elements.

Arrays in SAS are used primarily for iteratively processing like variables together. SAS/IML is a closer analog to NumPy arrays. SAS/IML is outside the scope of these examples.

    /******************************************************/
    /* c04_array_random_values.sas                        */
    /******************************************************/
    4         data _null_;
    5         
    6         call streaminit(54321);
    7         
    8         array s2 {10} ;
    9         do i = 1 to 10;
    10           s2{i} = rand("Uniform");
    11        
    12           if i <= 5 then put
    13              s2{i};
    14        end;

    0.4322317772
    0.5977982976
    0.7785986473
    0.1748250183
    0.3941470125
A Series can have a list of index labels.

```
In [3]:
s2 = Series(randn(10), index=['a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'i', 'j'])
print(s2.head(5))
a    1.057676
b    0.154904
c   -1.358674
d   -1.661059
e    2.111150
dtype: float64
The Series is indexed by integer value with the start position at 0.

```
In [4]:
print(s2[0])
1.05767568654
The SAS example uses a DO loop as the index subscript into the array.

    /******************************************************/
    /* c04_return_1st_array_element.sas                   */
    /******************************************************/
    4         data _null_;
    5         
    6         call streaminit(54321);
    7         
    8         array s2 {10} ;
    9         do i = 1 to 10;
    10           s2{i} = rand("Uniform");
    11        
    12           if i = 1 then put
    13              s2{i};
    14        end;

    0.4322317772
Return the first 3 elements in the Series.

```
In [5]:
print(s2[:3])
a    1.057676
b    0.154904
c   -1.358674
dtype: float64
    /******************************************************/
    /* c04_return_first_3_array_elements.sas              */
    /******************************************************/
    20        data _null_;
    21        
    22        call streaminit(54321);
    23        
    24        array s2 {10} ;
    25        do i = 1 to 10;
    26           s2{i} = rand("Uniform");
    27        
    28           if i <= 3 then put
    29              s2{i};
    30        end;

    0.4322317772
    0.5977982976
    0.7785986473
The example has two operations. The s2.mean() method calculates mean followed by a boolen test less than this calculated mean.

```
In [6]:
s2[s2 < s2.mean()]
```

Out[6]:
c   -1.358674
d   -1.661059
g   -0.426155
h   -0.841126
dtype: float64
Series and other objects have attributes using a dot (.) chaining-style syntax. .name is one a number of attributes for the Series object.

```
In [7]:
s2.name='Arbitrary Name'
print(s2.head(5))
a    1.057676
b    0.154904
c   -1.358674
d   -1.661059
e    2.111150
Name: Arbitrary Name, dtype: float64

## DataFrames

DataFrames are relational-like structures with labels. Alternatively, a DataFrame with a single column is a Series.

Like SAS, DataFrames have different methods for creation. DataFrames can be created by loading values from other Python objects. Data values can also be loaded from a range of non-Python input sources, including .csv files, DBMS tables, Web API's, and even SAS data sets (.sas7bdat), etc. Details are discussed in Chapter 11 -- Panda Readers

Start by reading the UK_Accidents .csv file. It contains vehicular accident data in the U.K from January 1, 2015 to December 31, 2015. The .csv file is located here.

There are multiple reports for each day of the year. The values are mostly integer values using the Road-Accident_Safety-Data_Guide.xls file found here to map values to descriptive labels.

## pd.read_csv()

The default values are used in the example below. Pandas provide a number of readers having parameters for controling missing values, date parsing, line skipping, data type mapping, etc. These parameters are analogous to SAS' INFILE/INPUT processing.

Additional examples of reading various data inputs into a DataFrame are covered in Chapter 11 -- Panda Readers

Notice the backslash (\) to normalize the Window's path name.

```
In [8]:
file_loc2 = "data/uk_accidents.csv"
df = pd.read_csv(file_loc2, low_memory=False)
PROC IMPORT is used to read the same .csv file. This is one of several methods for SAS to read a .csv file. Here we have taken the defaults.

    /******************************************************/
    /* c04_read_csv_proc_import.sas                       */
    /******************************************************/
    5  proc import datafile='c:\data\uk_accidents.csv' out=uk_accidents;

    NOTE: The file 'c:\data\uk_accidents.csv' is:
          File Name 'c:\data\uk_accidents.csv',
          Lrecl=32760, Recfm=V

    NOTE: 266776 records were read from file 'c:\data\uk_accidents.csv'
          The minimum record length was 65
          The maximum record length was 77
    NOTE: Data set "WORK.uk_accidents" has 266776 observation(s) and 27 variable(s)
Unlike SAS, the Python interpreter is mainly silent upon normal execution. When debugging it is helpful to invoke methods and functions to return information about these objects. This is somewhat analogous to use PUT statements in the SAS log to examine variable values.

The size, shape, and ndim attributes report respectively, number of cells, rows/columns, and number of dimensions are shown below.

```
In [9]:
print(df.size, df.shape, df.ndim)
7202952 (266776, 27) 2

## Read Verification

After reading a file, you often want to understand its content and structure. The DataFrame .info() method returns descriptions of the DataFrame's attributes. In SAS, this same information is generally found in the output from PROC CONTENTS.

```
In [10]:
df.info()
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 266776 entries, 0 to 266775
Data columns (total 27 columns):
Accident_Severity               266776 non-null int64
Number_of_Vehicles              266776 non-null int64
Number_of_Casualties            266776 non-null int64
Day_of_Week                     266776 non-null int64
Time                            266752 non-null object
Road_Type                       266776 non-null int64
Speed_limit                     266776 non-null int64
Junction_Detail                 266776 non-null int64
Light_Conditions                266776 non-null int64
Weather_Conditions              266776 non-null int64
Road_Surface_Conditions         266776 non-null int64
Urban_or_Rural_Area             266776 non-null int64
Vehicle_Reference               266776 non-null int64
Vehicle_Type                    266776 non-null int64
Skidding_and_Overturning        266776 non-null int64
Was_Vehicle_Left_Hand_Drive_    266776 non-null int64
Sex_of_Driver                   266776 non-null int64
Age_of_Driver                   266776 non-null int64
Engine_Capacity__CC_            266776 non-null int64
Propulsion_Code                 266776 non-null int64
Age_of_Vehicle                  266776 non-null int64
Casualty_Class                  266776 non-null int64
Sex_of_Casualty                 266776 non-null int64
Age_of_Casualty                 266776 non-null int64
Casualty_Severity               266776 non-null int64
Car_Passenger                   266776 non-null int64
Date                            266776 non-null object
dtypes: int64(25), object(2)
memory usage: 55.0+ MB

```
In [11]:
Image(filename='output/contents1.JPG')
```

Out[11]:

![](./images_4/2.jpg)

```
In [12]:
Image(filename='output/contents2.JPG')
```

Out[12]:

![](./images_4/3.jpg)

## Inspection

panda have methods which used to inspect to data values. The DataFrame .head() method displays the first 5 rows by default. The .tail() method displays the last 5 rows by default. The row count value can be an arbitrary integer value such as:

# display the last 20 rows of the DataFrame
df.tail(20)

SAS uses the FIRSTOBS and OBS options with procedures to determine input observations. The SAS code to print the last 20 observations of the uk_accidents data set is:

proc print data=uk_accidents (firstobs = 266756);
```
In [13]:
df.head()

![](./images_4/4.jpg)


OBS=n in SAS determines the number of observations used as input.

    /******************************************************/
    /* c_04_display_1st_5_obs.sas                         */
    /******************************************************/
    39       proc print data = uk_accidents (obs=5);

    The output from PROC PRINT is not displayed here.
Scoping output by columns is shown in the cell below. The column list is analogous to the VAR statement in PROC PRINT. Note the double set of square brackets for this syntax. This is slicing operating by column label. The square braces ([]) is the slicing operator. The details are explained here

This example uses the slicing operator to request columns by labels. Slicers work along rows as well.

```
In [14]:
df[['Sex_of_Driver', 'Time']].head(10)

![](./images_4/5.jpg)

Notice the DataFrame default index (incrementing from 0 to 9). This is analogous to the SAS automatic variable _n_. Later, we illustrate using other columns in the DataFrame as the index.

Below is the SAS program to print the first 10 observations of a data set along with the variables 'sex_of_driver' and 'time'.

    /******************************************************/
    /* c04_scoping_obs_and_variables.sas                  */
    /******************************************************/
    40       proc print data = uk_accidents (obs=10);
    41          var sex_of_driver time;

    The output from PROC PRINT is not displayed here.

## Handling Missing Data

Before analyzing data a common task is dealing with missing data. pandas uses two designations to indicate missing data, NaN (not a number) and the Python None object.

Consider cells #15, #16, and #17 below. Cell #15 uses the Python None object to represent a missing value in the array. In turn, Python infers the data type for the array to be an object. Unfortuantely, the use of a Python None object with an aggregation function for arrays raises an error. Cell #17 addresses the error raised in cell #16.

```
In [15]:
s1 = np.array([32, None, 17, 109, 201])
s1
```

Out[15]:
array([32, None, 17, 109, 201], dtype=object)
```
In [16]:
s1.sum()
---------------------------------------------------------------------------
TypeError                                 Traceback (most recent call last)
<ipython-input-16-b615dd188243> in <module>()
----> 1 s1.sum()

C:\Users\randy\Anaconda3\lib\site-packages\numpy\core\_methods.py in _sum(a, axis, dtype, out, keepdims)
     30 
     31 def _sum(a, axis=None, dtype=None, out=None, keepdims=False):
---> 32     return umr_sum(a, axis, dtype, out, keepdims)
     33 
     34 def _prod(a, axis=None, dtype=None, out=None, keepdims=False):

TypeError: unsupported operand type(s) for +: 'int' and 'NoneType'
To aliviate the error raised above, use the np.nan (missing value indicator) in the array example below. Also notice how Python chose floating point (or up-casting) for the array compared to the same example two cells above.

```
In [17]:
s1 = np.array([32, np.nan, 17, 109, 201])
print(s1)
s1.dtype
[  32.   nan   17.  109.  201.]
```

Out[17]:
dtype('float64')
Not all arithmetic operations using NaN's will result in a NaN.

```
In [18]:
s1.mean()
```

Out[18]:
nan
Contrast the Python program in the cell above for calculating the mean of the array elements with the SAS example below. SAS excludes the missing value and utilizes the remaining array elements to calculate a mean.

    /******************************************************/
    /* c04_mean_of_array_elements.sas                     */
    /******************************************************/
    4         data _null_;
    5         
    6         array s2 {5} (32 . 17 109 201);
    7         avg = mean(of s2[*]);
    8         
    9         put avg;

    89.75

## Missing Value Identification

Returning to our DataFrame, we need an analysis of missing values for all the columns. Pandas provide four methods for the detection and replacement of missing values. They are:

![](./images_4/6.jpg)

We will look at each of these in detail below.

A typical SAS-programming approach to address the missing data analysis is to write a program to traverses all columns using counter variables with IF/THEN testing for missing values.

This can be along the lines of the example in cell #19 below. df.columns returns the sequence of column names in the DataFrame.

```
In [19]:
for col_name in df.columns:
    print (col_name, end="---->")
    print (sum(df[col_name].isnull()))
Accident_Severity---->0
Number_of_Vehicles---->0
Number_of_Casualties---->0
Day_of_Week---->0
Time---->24
Road_Type---->0
Speed_limit---->0
Junction_Detail---->0
Light_Conditions---->0
Weather_Conditions---->0
Road_Surface_Conditions---->0
Urban_or_Rural_Area---->0
Vehicle_Reference---->0
Vehicle_Type---->0
Skidding_and_Overturning---->0
Was_Vehicle_Left_Hand_Drive_---->0
Sex_of_Driver---->0
Age_of_Driver---->0
Engine_Capacity__CC_---->0
Propulsion_Code---->0
Age_of_Vehicle---->0
Casualty_Class---->0
Sex_of_Casualty---->0
Age_of_Casualty---->0
Casualty_Severity---->0
Car_Passenger---->0
Date---->0
While this give the desired results, there is a better approach.

As an aside, if you find yourself thinking of solving a pandas' operation (or Python for that matter) using iterative processing, stop and take a little time to do research. Chances are, a method or function already exists!

Case-in-point is illustrated below. It chains the .sum() attribute to the .isnull() attribute to return a count of the missing values for the columns in the DataFrame.

The .isnull() method returns True for missing values. By chaining the .sum() method to the .isnull() method it produces a count of the missing values for each columns.

```
In [20]:
df.isnull().sum()
```

Out[20]:
Accident_Severity                0
Number_of_Vehicles               0
Number_of_Casualties             0
Day_of_Week                      0
Time                            24
Road_Type                        0
Speed_limit                      0
Junction_Detail                  0
Light_Conditions                 0
Weather_Conditions               0
Road_Surface_Conditions          0
Urban_or_Rural_Area              0
Vehicle_Reference                0
Vehicle_Type                     0
Skidding_and_Overturning         0
Was_Vehicle_Left_Hand_Drive_     0
Sex_of_Driver                    0
Age_of_Driver                    0
Engine_Capacity__CC_             0
Propulsion_Code                  0
Age_of_Vehicle                   0
Casualty_Class                   0
Sex_of_Casualty                  0
Age_of_Casualty                  0
Casualty_Severity                0
Car_Passenger                    0
Date                             0
dtype: int64
To identify missing values the SAS example below uses PROC Format to bin missing and non-missing values. Missing values are represented by default as (.) for numeric and blank (' ') for character variables. Therefore, a user-defined format is needed for both types.

PROC FREQ is used with the automatic variables _CHARACTER_ and _NUMERIC_ to produce a frequency listing for each variable type.

Only a portion of the SAS output is shown since separate output is produced for each variable. As with the example in cell # 19 above, the 'time' variable is the only variable with missing values.

    /******************************************************/
    /* c04_find_missing_numerics_characters.sas           */
    /******************************************************/
    26       proc format;
    27        value $missfmt ' '='Missing' other='Not Missing';
    28        value  missfmt  . ='Missing' other='Not Missing';
    29       run;

    30       
    31       proc freq data=uk_accidents;
    32          format _CHARACTER_ $missfmt.;
    33          tables _CHARACTER_ / missing missprint nocum nopercent;
    34       
    35          format _NUMERIC_ missfmt.;
    36          tables _NUMERIC_ / missing missprint nocum nopercent;

```
In [21]:
Image(filename='output/freq.JPG')

![](./images_4/7.jpg)

Another method for detecting missing values is to search column-wise by using the axis=1 parameter to the chained attributes .isnull().any(). The operation is then performed along columns.

```
In [22]:
null_data = df[df.isnull().any(axis=1)]
null_data.head()

![](./images_4/8.jpg)



## Missing Value Replacement

The code below is used to render multiple objects side-by-side. It is from Essential Tools for Working With Data, by Jake VanderPlas found here . It displays the 'before' and 'after' effects of changes to objects together.

```
In [23]:
class display(object):
    """Display HTML representation of multiple objects"""
    template = """<div style="float: left; padding: 10px;">
    <p style='font-family:"Courier New", Courier, monospace'>{0}</p>{1}
    </div>"""
    def __init__(self, *args):
        self.args = args

    def _repr_html_(self):
        return '\n'.join(self.template.format(a, eval(a)._repr_html_())
                         for a in self.args)

    def __repr__(self):
        return '\n\n'.join(a + '\n' + repr(eval(a))
                           for a in self.args)
To illustrate the .fillna() method, consider the following to create a DataFrame.

```
In [24]:
df2 = pd.DataFrame([['cold','slow', np.nan, 2., 6., 3.], 
                    ['warm', 'medium', 4, 5, 7, 9],
                    ['hot', 'fast', 9, 4, np.nan, 6],
                    ['cool', None, np.nan, np.nan, 17, 89],
                    ['cool', 'medium', 16, 44, 21, 13],
                    ['cold', 'slow', np.nan, 29, 33, 17]],
                    columns=['col1', 'col2', 'col3', 'col4', 'col5', 'col6'],
                    index=(list('abcdef')))
display("df2")

![](./images_4/9.jpg)

```
In [25]:
df_tf = df2.isnull()
display("df2", "df_tf")

![](./images_4/10.jpg)

```
In [26]:
df3 = df2.dropna()
display("df2", "df3")
```

Out[26]:
df2

col1	col2	col3	col4	col5	col6
a	cold	slow	NaN	2.0	6.0	3.0
b	warm	medium	4.0	5.0	7.0	9.0
c	hot	fast	9.0	4.0	NaN	6.0
d	cool	None	NaN	NaN	17.0	89.0
e	cool	medium	16.0	44.0	21.0	13.0
f	cold	slow	NaN	29.0	33.0	17.0
df3

col1	col2	col3	col4	col5	col6
b	warm	medium	4.0	5.0	7.0	9.0
e	cool	medium	16.0	44.0	21.0	13.0
The .dropna() method also works along a column axis. axis = 1 or axis = 'columns' is equivalent.

```
In [27]:
df4 = df2.dropna(axis='columns')
display("df2", "df4")

![](./images_4/11.jpg)

Clearly this drops a fair amount of 'good' data. The thresh parameter allows you to specify a minimum of non-null values to be kept for the row or column. In this case, row 'd' is dropped because it contains only 3 non-null values.

```
In [28]:
df5 = df2.dropna(thresh=5)
display("df2", "df5")

![](./images_4/12.jpg)

Rather than dropping rows and columns, missing values can be imputed or replaced. The .fillna() method returns either a Series or a DataFrame with null values replaced. The example below replaces all NaN's with zero.

```
In [29]:
df6 = df2.fillna(0)
display("df2", "df6")

![](./images_4/13.jpg)

As you can see from the example in cell #28 above, the .fillna() method is applied to all DataFrame cells. We may not wish to have missing values in df['col2'] replaced with zeros since they are strings. The method is applied to a list of target columns using the .loc method. The details for .loc method are discussed in Chapter 05--Understanding Indexes .

```
In [30]:
df7 = df2[['col3', 'col4', 'col5', 'col6']].fillna(0)
display("df2", "df7")

![](./images_4/14.jpg)

An imputation method based on the mean value of df['col6'] is shown below. The .fillna() method finds and then replaces all occurences of NaN with this calculated value.

```
In [31]:
df8 = df2[["col3", "col4", "col5"]].fillna(df2.col6.mean())
display("df2", "df8")

![](./images_4/15.jpg)

The corresponding SAS program is shown below. The PROC SQL SELECT INTO clause stores the calculated mean for the variable "col6" into the macro variable &col6_mean. This is followed by a Data Step iterating the array 'x' for "col3 - col5" replacing missing values with &col6_mean.



A more detailed example of replacing missing values with group means is located here.

SAS/Stat has PROC MI for imputation of missing values with a range of methods described here. PROC MI is outside the scope of these examples.

    /******************************************************/
    /* c04_replace_missing_with_mean_values.sas           */
    /******************************************************/
    4         data df;
    5         infile cards dlm=',';
    6         
    7         input col1 $
    8               col2 $
    9               col3
    10              col4
    11              col5
    12              col6 ;
    13        
    14        datalines;
    15        cold, slow, ., 2, 6, 3
    16        warm, medium, 4, 5, 7, 9
    17        hot, fast, 9, 4, ., 6
    18        cool, , ., ., 17, 89
    19        cool,  medium, 16, 44, 21, 13
    20        cold, slow, . ,29, 33, 17
    21        ;;;;

    22        proc sql;
    23           select mean(col6) into :col6_mean
    24           from df;
    25        quit;

    26        
    27        data df2;
    28           set df;
    29         array x {3} col3-col5  ;
    30        
    31         do i = 1 to 3;
    32            if x(i) = . then x(i) = &col6_mean;
    33         end;
The .fillna(method='ffill') is a 'forward' fill method. NaN's are replaced by the adjacent cell above traversing 'down' the columns. Cell #32 below constrasts the DataFrame df2, created in cell #24 above with the DataFrame df9 created with the 'forward' fill method.

```
In [32]:
df9 = df2.fillna(method='ffill')
display("df2", "df9")

![](./images_4/16.jpg)

Simalarly, the .fillna(bfill) is a 'backwards' fill method. NaN's are replaced by the adjecent cell traversing 'up' the columns. Cell #32 constrasts the DataFrame df2, created in cell #23 above with the DataFrame df10 created with the 'backward' fill method.

```
In [33]:
df10 = df2.fillna(method='bfill')
display("df2", "df10")

![](./images_4/17.jpg)

Cell #34 contrasts DataFrame df9 created in cell #32 using the 'forward' fill method with DataFrame df10 created in cell #33 with the 'backward' fill method.

```
In [34]:
display("df9", "df10")

![](./images_4/18.jpg)

Before dropping the missing rows, calculate the portion of records lost in the accidents DataFrame, df created above.

```
In [35]:
print("{} records in the DataFrame will be dropped.".format(df.Time.isnull().sum()))
print('The portion of records dropped is {0:6.3%}'.format(df.Time.isnull().sum() / (len(df) - df.Time.isnull().sum())))
24 records in the DataFrame will be dropped.
The portion of records dropped is 0.009%
The .dropna() method is silent except in the case of errors. We can verify the DataFrame's shape after the method is applied.

```
In [36]:
print(df.shape)
df = df.dropna()
print(df.shape)
(266776, 27)
(266752, 27)

## Resources

10 Minutes to pandas from pandas.pydata.org.

Tutorials , and just below this link is the link for the pandas Cookbook, from the pandas 0.19.0 documentation at pandas.pydata.org.

pandas Home page for Python Data Analysis Library.

Python Data Science Handbook , Essential Tools for Working With Data, by Jake VanderPlas.

pandas: Data Handling and Analysis in Python from 2013 BYU MCL Bootcamp documentation.

Intro to pandas data structures by Greg Reda. This is a three-part series using the Movie Lens data set nicely to illustrate pandas.

Cheat Sheet: The pandas DataFrame Object by Mark Graph and located at the University of Idaho's web-site.

Working with missing data pandas 0.19.0 documentation.

