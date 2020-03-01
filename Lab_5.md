<img align="right" src="./logo-small.png">

# Lab : 

#### Pre-reqs:
- Google Chrome (Recommended)

#### Lab Environment
Notebooks are ready to run. All packages have been installed. There is no requirement for any setup.

**Note:** Elev8ed Notebooks (powered by Jupyter) will be accessible at the port given to you by your instructor. Password for jupyterLab : `1234`

All Notebooks are present in `work/PythonForSASUsers` folder. To copy and paste: use **Control-C** and to paste inside of a terminal, use **Control-V**

You can access jupyter lab at `<host-ip>:<port>/lab/workspaces/lab6_Indexes`

##### Run Notebook
Click notebook `Chapter 05 -- Understanding Indexes.ipynb` in jupterLab UI and run jupyter notebook.

## Chapter 05 -- Understanding Indexes

## Topics Covered

Indicies

.iloc indexer

Setting and Reseting Indicies

.loc indexer

Mixing .loc indexer with Boolean Operations

Altering DataFrame values using the .loc indexer

Conditionally Apply Values Based on Another Column Value

.ix indexer

Indexing Issues

Resources

SAS users tend to think of indexing SAS data sets as a means to improve query performance. Another use case for using SAS indices is in performing non-sequential reads for table look-ups.

Indexing for DataFrames is used to provide direct access to data. Many analytical techniques take advantage of indexes. Indexing is also used as key values to selecting and subseting data.

It took me a bit of time to understand how indexing for Series and DataFrames actually work.

Many of the working examples I found, while mostly useful, used synthetic data which is typically ordered neatly. Here we examine issues like overlapping date ranges or index values in non-alphabetical or in non-sequential order and so on.

That is why you will find some errors in the examples below. By examining the pits I have fallen into, hopefully you can avoid them.

In [1]:
import numpy as np
import pandas as pd
from numpy.random import randn
from pandas import Series, DataFrame, Index
Consider the creation of the DataFrame df in the cell below.

In [2]:
df = pd.DataFrame([['a', 'cold','slow', np.nan, 2., 6., 3.], 
                    ['b', 'warm', 'medium', 4, 5, 7, 9],
                    ['c', 'hot', 'fast', 9, 4, np.nan, 6],
                    ['d', 'cool', None, np.nan, np.nan, 17, 89],
                    ['e', 'cool', 'medium', 16, 44, 21, 13],
                    ['f', 'cold', 'slow', np.nan, 29, 33, 17]])
In the 'df' DataFrame created above we did not specify a row index or column names resulting in the RangeIndex object used for row labels. Default column labels are created as well using another RangeIndex object.

In [3]:
df

![](.\images_5\1.jpg)

## Indices

The .index attribute returns the DataFrame's index structure. We did not explicitly set an index. As a result a default index object is created. The RangeIndex object has a start position at 0 and the end position set to len(df) - 1.

Return the row index.

In [4]:
df.index
Out[4]:
RangeIndex(start=0, stop=6, step=1)
Return the column labels. Since no labels were specificed, a RangeIndex object is used to identify columns.

In [5]:
df.columns
Out[5]:
RangeIndex(start=0, stop=7, step=1)
For observations SAS uses the automatic variable _N_ in the Data Step and FIRSTOBS and OBS in PROC step for its row indexing.

SAS also has a similar construct (SAS variable list) allowing the use of column name 'ranges' (col1-colN) described here.

The SAS example below creates a data set with same data used to create the DataFrame df in cell #2 above.

    /******************************************************/
    /* c05_create_analog_df_dataframe.sas                 */
    /******************************************************/
    11      data df;
    12      infile cards dlm=',';
    13      
    14      input id   $
    15            col1 $
    16            col2 $
    17            col3
    18            col4
    19            col5
    20            col6 ;
    21      
    22      datalines;
    23      a, cold, slow, ., 2, 6, 3
    24      b, warm, medium, 4, 5, 7, 9
    25      c, hot, fast, 9, 4, ., 6
    26      d, cool, , ., ., 17, 89
    27      e, cool,  medium, 16, 44, 21, 13
    28      f, cold, slow, . ,29, 33, 17
    29      ;;;;
The Data Step with the SET options NOBS= is an example of implicit indexing used by SAS. The END= parameter on the SET statement is initialized to 0 and set to one when the last observation is read. The automatic variable _N_ can be used as the observation (row) index.

    /******************************************************/
    /* c05_sas_row_index1.sas                             */
    /******************************************************/
    31      data _null_;
    32      set df nobs=obs end=end_of_file;
    33      
    34      put _n_ = ;
    35      
    36      if end_of_file then
    37         put 'Data set contains: ' obs ' observations' /;

    _N_=1
    _N_=2
    _N_=3
    _N_=4
    _N_=5
    _N_=6
    Data set contains: 6  observations
Using the DataFrame indices we can select specific rows and columns. DataFrames provide indexers to accomplish these tasks. They are:

1. .iloc() method which is mainly an integer-based method
2. .loc() method used to select ranges by labels (either column or row)
3. .ix() method which supports a combination of the loc() and iloc() methods

We also illsutrate altering values in a DataFrame using the .loc(). This is equivalent to the SAS update method.

## .iloc Indexer

The .iloc indexer uses an integer-based method for locating row and column positions. It is a very robust indexer, however, it is limited by the fact that humans are better at remembering labels than numbers. This is analogous to locating an observations by _n_ or locating a SAS variable using 'colN' from a variable list.

The syntax for the .iloc indexer is:

df.iloc[row selection, column selection]

A comma (,) is used to separate the request of rows for the request for columns. A colon (:) is used to request a range of cells.

The absence of a either a column or row selection is an implicit request for all columns or rows, respectively.

Details for the .iloc indexer are located here.

.iloc[0] is useful since it returns the first scalar from a Series of the first column from a DataFrame.

In [6]:
df.iloc[0]
Out[6]:
0       a
1    cold
2    slow
3     NaN
4       2
5       6
6       3
Name: 0, dtype: object
The point= option on the SET statement behaves similarly to return the first row in the data set. Note the SET statement inside a DO loop and the STOP statement. STOP is needed because the POINT= option indicates a non-sequnetial access pattern and thus the end of data set indicator is not available.

    /******************************************************/
    /* c05_sas_row_index2.sas                             */
    /******************************************************/
    52      data _null_;
    53      
    54      do iloc = 1 to 1;
    55         set df point=iloc;
    56      
    57       put _all_ ;
    58       end;
    59      stop;

    _N_=1 _ERROR_=0 iloc=1 id=a col1=cold col2=slow col3=. col4=2 col5=6 col6=3
In the example below, you might expect three rows returned, rather than two. The range request for .iloc[] includes the start of the range and does not include the last item in the range value.

In [7]:
df.iloc[2:4]

![](.\images_5\2.jpg)

The SAS analog example for cell #6 is below.

    /******************************************************/
    /* c05_sas_row_index3.sas                             */
    /******************************************************/
    94       data _null_ ;
    95       
    96       do iloc = 3 to 4;
    97           set df point=iloc;
    98          put _all_ ;
    99       end;
    100      stop;

    _N_=1 _ERROR_=0 iloc=3 id=c col1=hot col2=fast col3=9 col4=4 col5=. col6=6
    _N_=1 _ERROR_=0 iloc=4 id=d col1=cool col2=  col3=. col4=. col5=17 col6=89
Similar to the indexer for string slicing, the index position

iloc[0]

returns the first row in a DataFrame and

iloc[-1]

returns the last row in the DataFrame. This is analogous to the END= option for the SET statement (assuming a sequential access pattern).

The .iloc indexer is mainly used to locate first or last row in the DataFrame.

In [8]:
df.iloc[-1]
Out[8]:
0       f
1    cold
2    slow
3     NaN
4      29
5      33
6      17
Name: 5, dtype: object
The .iloc indexer in cell #8 below returns rows 2 and 3 using (2:4) as the row selector and columns 0 to 6 using (0:6) as the column selctor.

In [9]:
df.iloc[2:4, 0:6]

![](.\images_5\3.jpg)

The analog SAS program for returning the same sub-set is below. FIRSTOBS=3 OBS=4 is the equivalent row selector and keep = id -- col5 is the equivalent column selector.

    /******************************************************/
    /* c05_firstobs_3_obs_4.sas                           */
    /******************************************************/
    60      data df;
    61          set df(keep = id -- col5
    62                 firstobs=3 obs=4);
    63       put _all_ ;

    _N_=1 _ERROR_=0 id=c col1=hot col2=fast col3=9 col4=4 col5=.
    _N_=2 _ERROR_=0 id=d col1=cool col2=  col3=. col4=. col5=17
The .iloc idexer illustrating multi-row and multi-column requests. Note the double square brackets ([]) syntax.

In [10]:
df.iloc[[1,3,5], [2, 4, 6]]

![](.\images_5\4.jpg)

## .loc Indexer
The .loc indexer is similar to .iloc and allows access to rows and columns by labels. A good analogy is a cell reference in Excel, eg. C:31.

The syntax for the .loc indexer is:

df.loc[row selection, column selection]

For both the row and column selection, a comma (,) is used to request a list of multiple cells. A colon (:) is used to request a range of cells.

Similiar to the .iloc indexer you can select combinations of rows and columns. The doc details are located here.

Consider the DataFrame df2 created below in cell #10. It contains the new columns 'id' and 'date'.

In [11]:
df2 = pd.DataFrame([['a', 'cold','slow', np.nan, 2., 6., 3., '08/01/16'], 
                    ['b', 'warm', 'medium', 4, 5, 7, 9, '03/15/16'],
                    ['c', 'hot', 'fast', 9, 4, np.nan, 6, '04/30/16'],
                    ['d', 'None', 'fast', np.nan, np.nan, 17, 89, '05/31/16'],
                    ['e', 'cool', 'medium', 16, 44, 21, 13, '07/04/16'],
                    ['f', 'cold', 'slow', np.nan, 29, 33, 17, '01/01/16']],
                    columns=['id', 'col1', 'col2', 'col3', 'col4', 'col5', 'col6', 'date'])
Executing just the name for the DataFrame is the equivalent of:

print(df2)

The print() method for a DataFrame returns the output without the cell outlines, however.

In [12]:
df2

![](.\images_5\5.jpg)

## Setting and resetting Indicies

In SAS the construction of an index for a data set creates an external file used in a deterministic fashion by SAS. In contrast, the construction of a panda index physically alters either the DataFrame, or a copy of it, depending on argument values to the set_index method. The doc is located here .

Start by setting the index to 'id', to access rows by a row value or a range of rows values. By default, the column is dropped when it becomes the index. You may not want this behavior, in which case you set the drop= argument value to False.

drop=False

This is useful if you have multiple occasions setting and resetting the index. Otherwise, a re-read of the DataFrame is required. Below, use the set_index() method to set the index to the 'id' column.

In [13]:
df2.set_index('id', inplace=True, drop=False)
The .set_index attribute execution is silent when the inplace=argument value is utilized. Validate the index using the .index attribute.

In [14]:
df2.index
Out[14]:
Index(['a', 'b', 'c', 'd', 'e', 'f'], dtype='object', name='id')
To reset the index, use the reset_index() method.

In [15]:
df2 = df2.reset_index(drop=True)
In order to have the remainder of the examples for the .loc indexer to work, we set the index again.

In [16]:
df2.set_index('id', inplace=True, drop=False)
Return the row labeled 'e'.

In [17]:
df2.loc['e', ]
Out[17]:
id             e
col1        cool
col2      medium
col3          16
col4          44
col5          21
col6          13
date    07/04/16
Name: e, dtype: object
Return rows in the range of 'b' to 'f' inclusive. 'b':'f' denotes a row range. The absence of a column request is an implicit request for all of them.

In [18]:
df2.loc['b':'f' ,]

![](.\images_5\6.jpg)

Return the rows between range of 'd' to 'f' inclusive. 'col6' and 'col2' is a request for columns by label.

In [19]:
df2.loc['d':'f',['col6','col2']]

![](.\images_5\7.jpg)

Return the 'date' column by label.

In [20]:
df2.loc[: , 'date']
Out[20]:
id
a    08/01/16
b    03/15/16
c    04/30/16
d    05/31/16
e    07/04/16
f    01/01/16
Name: date, dtype: object
Change the DataFrame df2 index from 'id' to 'date'. The inplace=True argument does not make a copy of the DataFrame.

In [21]:
df2.set_index('date', inplace=True)
Validate the index.

In [22]:
df2.index
Out[22]:
Index(['08/01/16', '03/15/16', '04/30/16', '05/31/16', '07/04/16', '01/01/16'], dtype='object', name='date')
Request a row by label.

In [23]:
df2.loc['05/31/16']
Out[23]:
id         d
col1    None
col2    fast
col3     NaN
col4     NaN
col5      17
col6      89
Name: 05/31/16, dtype: object
Request arbitrary rows. Notice the double square braces ([])

In [24]:
df2.loc[['03/15/16', '07/04/16']]

![](.\images_5\8.jpg)

Request a range of rows.

In [25]:
df2.loc['04/30/16':'07/04/16',['col2','col1']]

![](.\images_5\9.jpg)

The SAS program below is equivalent to cell #25 above. It uses character values representing dates.

    /******************************************************/
    /* c05_select_dates_01Feb16_and_31Jul16.sas           */
    /******************************************************/
    25       data df2(keep = col2 col1);
    26          set df(where=(date between '04/30/16' and '07/04/16'));
    27       put _all_;

    _N_=1 _ERROR_=0 id=c col1=hot col2=fast col3=9 col4=4 col5=. col6=6 date=04/30/16
    _N_=2 _ERROR_=0 id=d col1=cool col2=  col3=. col4=. col5=17 col6=89 date=05/31/16
    _N_=3 _ERROR_=0 id=e col1=cool col2=medium col3=16 col4=44 col5=21 col6=13 date=07/04/16
In cell #26 below we hit a snag. The issue begins with cell #21 above using the set_index attribute for the 'df2' DataFrame. Examine cell #22 above to observe how the df2['date'] column dtype is 'object'.

This indicates we are working with string literals and not datetime objects. Cells #24 and #25 work because these specific labels are values found in the df2['date'] index.

Cell #26, below does not work, since the range request contains the value '07/31/16 as the range end-point which is not a value in the index.

The remedy, shown below in cell #29 is to use the pd.to_datetime() method to convert the df2['date'] string values into a datetime object. The obvious analogy for SAS users is converting a string variable to a numeric variable which has an associated date format.

Converting string literals to datetime values is covered in the section, String Literal Mapped to datetime timestamp located here.

In [26]:
df2.loc['01/01/16':'07/31/16']
---------------------------------------------------------------------------
ValueError                                Traceback (most recent call last)
C:\Users\randy\Anaconda3\lib\site-packages\pandas\indexes\base.py in get_slice_bound(self, label, side, kind)
   2909             try:
-> 2910                 return self._searchsorted_monotonic(label, side)
   2911             except ValueError:

C:\Users\randy\Anaconda3\lib\site-packages\pandas\indexes\base.py in _searchsorted_monotonic(self, label, side)
   2875 
-> 2876         raise ValueError('index must be monotonic increasing or decreasing')
   2877 

ValueError: index must be monotonic increasing or decreasing

During handling of the above exception, another exception occurred:

KeyError                                  Traceback (most recent call last)
<ipython-input-26-e06fa69430df> in <module>()
----> 1 df2.loc['01/01/16':'07/31/16']

C:\Users\randy\Anaconda3\lib\site-packages\pandas\core\indexing.py in __getitem__(self, key)
   1294             return self._getitem_tuple(key)
   1295         else:
-> 1296             return self._getitem_axis(key, axis=0)
   1297 
   1298     def _getitem_axis(self, key, axis=0):

C:\Users\randy\Anaconda3\lib\site-packages\pandas\core\indexing.py in _getitem_axis(self, key, axis)
   1435         if isinstance(key, slice):
   1436             self._has_valid_type(key, axis)
-> 1437             return self._get_slice_axis(key, axis=axis)
   1438         elif is_bool_indexer(key):
   1439             return self._getbool_axis(key, axis=axis)

C:\Users\randy\Anaconda3\lib\site-packages\pandas\core\indexing.py in _get_slice_axis(self, slice_obj, axis)
   1316         labels = obj._get_axis(axis)
   1317         indexer = labels.slice_indexer(slice_obj.start, slice_obj.stop,
-> 1318                                        slice_obj.step, kind=self.name)
   1319 
   1320         if isinstance(indexer, slice):

C:\Users\randy\Anaconda3\lib\site-packages\pandas\indexes\base.py in slice_indexer(self, start, end, step, kind)
   2783         """
   2784         start_slice, end_slice = self.slice_locs(start, end, step=step,
-> 2785                                                  kind=kind)
   2786 
   2787         # return a slice

C:\Users\randy\Anaconda3\lib\site-packages\pandas\indexes\base.py in slice_locs(self, start, end, step, kind)
   2968         end_slice = None
   2969         if end is not None:
-> 2970             end_slice = self.get_slice_bound(end, 'right', kind)
   2971         if end_slice is None:
   2972             end_slice = len(self)

C:\Users\randy\Anaconda3\lib\site-packages\pandas\indexes\base.py in get_slice_bound(self, label, side, kind)
   2911             except ValueError:
   2912                 # raise the original KeyError
-> 2913                 raise err
   2914 
   2915         if isinstance(slc, np.ndarray):

C:\Users\randy\Anaconda3\lib\site-packages\pandas\indexes\base.py in get_slice_bound(self, label, side, kind)
   2905         # we need to look up the label
   2906         try:
-> 2907             slc = self.get_loc(label)
   2908         except KeyError as err:
   2909             try:

C:\Users\randy\Anaconda3\lib\site-packages\pandas\indexes\base.py in get_loc(self, key, method, tolerance)
   1945                 return self._engine.get_loc(key)
   1946             except KeyError:
-> 1947                 return self._engine.get_loc(self._maybe_cast_indexer(key))
   1948 
   1949         indexer = self.get_indexer([key], method=method, tolerance=tolerance)

pandas\index.pyx in pandas.index.IndexEngine.get_loc (pandas\index.c:4154)()

pandas\index.pyx in pandas.index.IndexEngine.get_loc (pandas\index.c:4018)()

pandas\hashtable.pyx in pandas.hashtable.PyObjectHashTable.get_item (pandas\hashtable.c:12368)()

pandas\hashtable.pyx in pandas.hashtable.PyObjectHashTable.get_item (pandas\hashtable.c:12322)()

KeyError: '07/31/16'
Reset the index for the DataFrame 'df2' to the default RangeIndex object.

In [27]:
df2.reset_index(inplace=True)
Validate the index.

In [28]:
df2.index
Out[28]:
RangeIndex(start=0, stop=6, step=1)
Cast the df2['date'] column from dtype='object' (strings) to dtype=datetime.

In [29]:
df2['date'] = pd.to_datetime(df2.date)
Set the df2['date'] column as the index.

In [30]:
df2.set_index('date', inplace=True)
Validate the index. Observe the dytpe is now datetime64--a datetime stamp. See Chapter 8--Date, Time, and Timedelta Objects for more details on datetime arithmetic, shifting time intervals, and determining durations.

In [31]:
df2.index
Out[31]:
DatetimeIndex(['2016-08-01', '2016-03-15', '2016-04-30', '2016-05-31',
               '2016-07-04', '2016-01-01'],
              dtype='datetime64[ns]', name='date', freq=None)
With the 'ds2['date'] column values converted to a datetime values, re-do the statement in cell #26 above.

In [32]:
df2.loc['02/01/16':'07/31/16']

![](.\images_5\10.jpg)

The SAS example below illustrates a similiar set of steps:

1.  Read the original 'date' variable which is character and rename it to 'str_date'
2.  Use the input funtion to 'read' the 'str_date' values and assign them to the 'date' variable using the mmddyy10. informat
3.  Print the date value without any formatting showing it is now a SAS datetime value
4.  Print the SAS datetime value using the mmddyy10. date format
    /******************************************************/
    /* c05_alter_string_to_datetime_variable.sas          */
    /******************************************************/
    4      data df2(drop = str_date);
    5         set df(rename=(date=str_date));
    6      date=input(str_date,mmddyy10.);
    7      
    8      if _n_ = 1 then put date= /
    9         date mmddyy10.;

    date=20667
    08/01/2016

## Mixing .loc Indexer with Boolean Operators

This approach works by creating either a Series or array of boolean values (True or False). This Series or array is then used by the .loc indexer to return all of the values that evaluate to True. Using the DataFrame df2 created in the cell above, consider the following.

We want to return all rows where 'col2' is not equal to 'fast. This is expressed as:

df2['col2'] != 'fast'

A Series is returned with the True/False values not equal to 'fast' for fd2['col2'] shown below. The df2['date'] column is returned since it remains as the index for the DataFrame. The second print() method displays this object as being derived from the class: Series.

In [33]:
print(df2['col2'] != 'fast')
print(type(df2['col2'] != 'fast'))
date
2016-08-01     True
2016-03-15     True
2016-04-30    False
2016-05-31    False
2016-07-04     True
2016-01-01     True
Name: col2, dtype: bool
<class 'pandas.core.series.Series'>
Passing the boolean Series:

df2['col2'] != 'fast' 

to the .loc indexer to retrieve those rows with a boolean value of True. Also request 'col1' and 'col2', which a request by label.

In [34]:
df2.loc[df2['col2'] != 'fast', 'col1':'col2']

![](.\images_5\11.jpg)

You can combine any number of boolean operation together. Boolean value comparison operators are documented here.

In [35]:
df2.loc[(df2.col3 >=  9) & (df2.col1 == 'cool'), ]

![](.\images_5\12.jpg)

The .isin() method returns a boolean vector similar to the behavior described in cell #34 above. In this example, the .isin list of elements evaluates True if the listed elements are found by the .loc indexer in 'col6'.

In [36]:
df2.loc[df2.col6.isin([6, 9, 13])]

![](.\images_5\13.jpg)

So far, the .loc indexers have resulted in an output stream. All of the indexers can be used to sub-set a DataFrame using assignment syntax shown in the cell #33 below.

In [37]:
df3 = df2.loc[df2.col6.isin([6, 9, 13])]
df3

![](.\images_5\14.jpg)

Generally, in these types of sub-setting operations, the shape of the extracted DataFrame will be smaller than the input DataFrame.

To return a DataFrame of the same shape as the original, use the where() method. Details for the pandas where() method is described here.

In [38]:
print('Shape for df2 is', df2.shape)
print('Shape for df3 is', df3.shape)
Shape for df2 is (6, 7)
Shape for df3 is (3, 7)
The example SAS program below uses the WHERE IN (list) syntax to subset a data set analogous to the example in cell #32 above.

    /******************************************************/
    /* c05_where_in.sas                                   */
    /******************************************************/
    NOTE: Data set "WORK.df2" has 6 observation(s) and 8 variable(s)

    27      data df3;
    28         set df2(where=(col6 in (6 9 13)));

    NOTE: 3 observations were read from "WORK.df2"
    NOTE: Data set "WORK.df3" has 3 observation(s) and 8 variable(s)
Notice how the SAS variable count and DataFrame column count differ by 1. That is because the DataFrame .shape() method does not include the index as part of its column count. By reseting the index, the SAS variable count and DataFrame columns count agree.

In [39]:
df3.reset_index(inplace=True)
print('Shape for df3 is', df3.shape)
Shape for df3 is (3, 8)

## Altering DataFrame values using the .loc indexer

The .loc indexer can also be used to do an in-place update of values.

Find the values for df2['col2'] column before updating.

In [40]:
df2.loc[: , 'col2']
Out[40]:
date
2016-08-01      slow
2016-03-15    medium
2016-04-30      fast
2016-05-31      fast
2016-07-04    medium
2016-01-01      slow
Name: col2, dtype: object

## Conditionaly Apply Values Based on Another Column Value

In [41]:
df2.loc[df2['col6'] > 50, "col2"] = "VERY FAST"
The values for df2['col2'] column after the update.

In [42]:
df2.loc[: , 'col2']
Out[42]:
date
2016-08-01         slow
2016-03-15       medium
2016-04-30         fast
2016-05-31    VERY FAST
2016-07-04       medium
2016-01-01         slow
Name: col2, dtype: object

## .ix Indexer

The .ix indexer combines characteristics of the .loc and .iloc indexers. This means you can select rows and columns by labels and integers.

The syntax for the .ix indexer is:

df.ix[row selection, column selection]

For both the row and column selection, a comma (,) is used to request a list of multiple cells. A colon (:) is used to request a range of cells.

Similar to the .loc indexer you can select combinations of rows and columns.

The .ix indexer is sometimes tricky to use. A good rule of thumb is if you are indexing using labels or indexing using integers, use the .loc and .iloc to avoid unexpected results. The documentation details are found here.

Consider the creation of the DataFrame 'df4' constructed in the cell below. It is similar to DataFrame df2 created in cell #10 above. The differences are the addition of another column and columns being identified with labels as well as integers.

In [43]:
df4 = pd.DataFrame([['a', 'cold','slow', np.nan, 2., 6., 3., 17, '08/01/16'], 
                    ['b', 'warm', 'medium', 4, 5, 7, 9, 21, '03/15/16'],
                    ['c', 'hot', 'fast', 9, 4, np.nan, 6, 10, '04/30/16'],
                    ['d', 'None', 'fast', np.nan, np.nan, 17, 89, 44, '05/31/16'],
                    ['e', 'cool', 'medium', 16, 44, 21, 13, 99, '07/04/16'],
                    ['f', 'cold', 'slow', np.nan, 29, 33, 17, 11,'01/01/16']],
                    columns=['id', 'col1', 'col2', 'col3', 'col4', 4, 5, 6, 'date'])
In [44]:
df4

![](.\images_5\15.jpg)

Set the index to column df4['id'].

In [45]:
df4.set_index('id', inplace=True)
The .ix indexer allows slicing by labels and integer poitions along the index. And look closely at the results since the request for columns is based on the integer-position of the column. The column requestor of 6 is not a column label, but its position.

In [46]:
df4.ix['b':'e', 6:8]

![](.\images_5\16.jpg)

## A Review:

.iloc uses the integer position in the index and only accepts integers
.loc uses the labels in the index
.ix generally behaves like the .loc

Finally, to appreciate the differences, consider the following DataFrame. Also notice the un-Pythonic style of using a semi-colon at the end of the DataFrame definition.

In [47]:
df5 = pd.DataFrame([['a', 'cold','slow', np.nan, 2., 6., 3.], 
                    ['b', 'warm', 'medium', 4, 5, 7, 9],
                    ['c', 'hot', 'fast', 9, 4, np.nan, 6],
                    ['d', 'cool', None, np.nan, np.nan, 17, 89],
                    ['e', 'cool', 'medium', 16, 44, 21, 13],
                    ['f', 'cold', 'slow', np.nan, 29, 33, 17]],
                    index = [6, 8, 2, 3, 4, 5,]);
df5

![](.\images_5\17.jpg)

The .iloc indexer returns the first two rows since it looks at positions.

In [48]:
df5.iloc[:2]

![](.\images_5\18.jpg)

The .loc indexer returns 3 rows since it looks at the labels.

In [49]:
df5.loc[:2]

![](.\images_5\19.jpg)

The .ix indexer returns the same number of rows as the .loc indexer since its behavior is to first use labels before looking by position. Looking by position with an integer-based index can lead to unexpected results. This illustrated in cell #49 below.

In [50]:
df5.ix[:2]

![](.\images_5\20.jpg)

For the next two examples, review the DataFrame df5 index structure in the cell below.

In [51]:
df5.index

![](.\images_5\21.jpg)

The .iloc example in the cell below returns the first row. That's because it is looking by position.

In [52]:
df5.iloc[:1]

![](.\images_5\22.jpg)

The .ix example in the cell below raises a KeyError since 1 is not found in the index.

In [53]:
df5.ix[:1]
---------------------------------------------------------------------------
ValueError                                Traceback (most recent call last)
C:\Users\randy\Anaconda3\lib\site-packages\pandas\indexes\base.py in get_slice_bound(self, label, side, kind)
   2909             try:
-> 2910                 return self._searchsorted_monotonic(label, side)
   2911             except ValueError:

C:\Users\randy\Anaconda3\lib\site-packages\pandas\indexes\base.py in _searchsorted_monotonic(self, label, side)
   2875 
-> 2876         raise ValueError('index must be monotonic increasing or decreasing')
   2877 

ValueError: index must be monotonic increasing or decreasing

During handling of the above exception, another exception occurred:

KeyError                                  Traceback (most recent call last)
<ipython-input-53-bf1757e6e2b5> in <module>()
----> 1 df5.ix[:1]

C:\Users\randy\Anaconda3\lib\site-packages\pandas\core\indexing.py in __getitem__(self, key)
     76         else:
     77             key = com._apply_if_callable(key, self.obj)
---> 78             return self._getitem_axis(key, axis=0)
     79 
     80     def _get_label(self, label, axis=0):

C:\Users\randy\Anaconda3\lib\site-packages\pandas\core\indexing.py in _getitem_axis(self, key, axis)
    990         labels = self.obj._get_axis(axis)
    991         if isinstance(key, slice):
--> 992             return self._get_slice_axis(key, axis=axis)
    993         elif (is_list_like_indexer(key) and
    994               not (isinstance(key, tuple) and

C:\Users\randy\Anaconda3\lib\site-packages\pandas\core\indexing.py in _get_slice_axis(self, slice_obj, axis)
   1235         if not need_slice(slice_obj):
   1236             return obj
-> 1237         indexer = self._convert_slice_indexer(slice_obj, axis)
   1238 
   1239         if isinstance(indexer, slice):

C:\Users\randy\Anaconda3\lib\site-packages\pandas\core\indexing.py in _convert_slice_indexer(self, key, axis)
    190         # if we are accessing via lowered dim, use the last dim
    191         ax = self.obj._get_axis(min(axis, self.ndim - 1))
--> 192         return ax._convert_slice_indexer(key, kind=self.name)
    193 
    194     def _has_valid_setitem_indexer(self, indexer):

C:\Users\randy\Anaconda3\lib\site-packages\pandas\indexes\base.py in _convert_slice_indexer(self, key, kind)
   1077         else:
   1078             try:
-> 1079                 indexer = self.slice_indexer(start, stop, step, kind=kind)
   1080             except Exception:
   1081                 if is_index_slice:

C:\Users\randy\Anaconda3\lib\site-packages\pandas\indexes\base.py in slice_indexer(self, start, end, step, kind)
   2783         """
   2784         start_slice, end_slice = self.slice_locs(start, end, step=step,
-> 2785                                                  kind=kind)
   2786 
   2787         # return a slice

C:\Users\randy\Anaconda3\lib\site-packages\pandas\indexes\base.py in slice_locs(self, start, end, step, kind)
   2968         end_slice = None
   2969         if end is not None:
-> 2970             end_slice = self.get_slice_bound(end, 'right', kind)
   2971         if end_slice is None:
   2972             end_slice = len(self)

C:\Users\randy\Anaconda3\lib\site-packages\pandas\indexes\base.py in get_slice_bound(self, label, side, kind)
   2911             except ValueError:
   2912                 # raise the original KeyError
-> 2913                 raise err
   2914 
   2915         if isinstance(slc, np.ndarray):

C:\Users\randy\Anaconda3\lib\site-packages\pandas\indexes\base.py in get_slice_bound(self, label, side, kind)
   2905         # we need to look up the label
   2906         try:
-> 2907             slc = self.get_loc(label)
   2908         except KeyError as err:
   2909             try:

C:\Users\randy\Anaconda3\lib\site-packages\pandas\indexes\base.py in get_loc(self, key, method, tolerance)
   1945                 return self._engine.get_loc(key)
   1946             except KeyError:
-> 1947                 return self._engine.get_loc(self._maybe_cast_indexer(key))
   1948 
   1949         indexer = self.get_indexer([key], method=method, tolerance=tolerance)

pandas\index.pyx in pandas.index.IndexEngine.get_loc (pandas\index.c:4154)()

pandas\index.pyx in pandas.index.IndexEngine.get_loc (pandas\index.c:4018)()

pandas\hashtable.pyx in pandas.hashtable.Int64HashTable.get_item (pandas\hashtable.c:6610)()

pandas\hashtable.pyx in pandas.hashtable.Int64HashTable.get_item (pandas\hashtable.c:6554)()

KeyError: 1

## Indexing Issues

So far, so good. We have a basic understanding of how indexes can be established, utilized, and reset. We can use the .iloc, .loc, and .ix indexers to retrieve subsets of columns and rows. But what about real-world scenarios where data is rarely, if ever tidy?

The synthetic examples above work (except the intentional errors of course) since they rely on constructing the DataFrames in an orderly manner, like having 'id' columns in alphabetical order or dates in chronological order.

Consider the DataFrame 'df5' created below. It is similar to DataFrame 'df2' in cell #10 above with the exception of the df5['id'] column containing non-unique values.

In [54]:
df5 = pd.DataFrame([['b', 'cold','slow', np.nan, 2., 6., 3., '01/01/16'], 
                    ['c', 'warm', 'medium', 4, 5, 7, 9, '03/15/16'],
                    ['a', 'hot', 'fast', 9, 4, np.nan, 6, '04/30/16'],
                    ['d', 'cool', None, np.nan, np.nan, 17, 89, '05/31/16'],
                    ['c', 'cool', 'medium', 16, 44, 21, 13, '07/04/16'],
                    ['e', 'cold', 'slow', np.nan, 29, 33, 17, '08/30/16']],
                    columns=['id', 'col1', 'col2', 'col3', 'col4', 'col5', 'col6', 'date']);
df5

![](.\images_5\23.jpg)

Set the index for DataFrame df5 to the df5['id'] column.

In [55]:
df5.set_index('id', inplace=True)
Validate the index for DataFrame df5.

In [56]:
df5.index
Out[56]:
Index(['b', 'c', 'a', 'd', 'c', 'e'], dtype='object', name='id')
We can use the .loc indexer to request the rows in the range of 'b' through 'd'.

In [57]:
df5.loc['b':'d', :]

![](.\images_5\24.jpg)

If you look closely at the results from the example above, you will find the first occurence of the row 'id' label 'c' was returned, but not the second row labeled 'c'. 'id' label 'c' is obviously non-unique. And that is only part of the issue. Consider futher the use of a non-unique label for the row range selection in the example below.

This issue is described in sparse prose here.

If we should want the row label range of 'b' to 'c' with all the columns we raise the error:

"Cannot get right slice bound for non-unique label: 'c'"
In [58]:
df5.loc['b':'c', :]
---------------------------------------------------------------------------
KeyError                                  Traceback (most recent call last)
<ipython-input-58-64fc99b408b0> in <module>()
----> 1 df5.loc['b':'c', :]

C:\Users\randy\Anaconda3\lib\site-packages\pandas\core\indexing.py in __getitem__(self, key)
   1292 
   1293         if type(key) is tuple:
-> 1294             return self._getitem_tuple(key)
   1295         else:
   1296             return self._getitem_axis(key, axis=0)

C:\Users\randy\Anaconda3\lib\site-packages\pandas\core\indexing.py in _getitem_tuple(self, tup)
    802                 continue
    803 
--> 804             retval = getattr(retval, self.name)._getitem_axis(key, axis=i)
    805 
    806         return retval

C:\Users\randy\Anaconda3\lib\site-packages\pandas\core\indexing.py in _getitem_axis(self, key, axis)
   1435         if isinstance(key, slice):
   1436             self._has_valid_type(key, axis)
-> 1437             return self._get_slice_axis(key, axis=axis)
   1438         elif is_bool_indexer(key):
   1439             return self._getbool_axis(key, axis=axis)

C:\Users\randy\Anaconda3\lib\site-packages\pandas\core\indexing.py in _get_slice_axis(self, slice_obj, axis)
   1316         labels = obj._get_axis(axis)
   1317         indexer = labels.slice_indexer(slice_obj.start, slice_obj.stop,
-> 1318                                        slice_obj.step, kind=self.name)
   1319 
   1320         if isinstance(indexer, slice):

C:\Users\randy\Anaconda3\lib\site-packages\pandas\indexes\base.py in slice_indexer(self, start, end, step, kind)
   2783         """
   2784         start_slice, end_slice = self.slice_locs(start, end, step=step,
-> 2785                                                  kind=kind)
   2786 
   2787         # return a slice

C:\Users\randy\Anaconda3\lib\site-packages\pandas\indexes\base.py in slice_locs(self, start, end, step, kind)
   2968         end_slice = None
   2969         if end is not None:
-> 2970             end_slice = self.get_slice_bound(end, 'right', kind)
   2971         if end_slice is None:
   2972             end_slice = len(self)

C:\Users\randy\Anaconda3\lib\site-packages\pandas\indexes\base.py in get_slice_bound(self, label, side, kind)
   2922             if isinstance(slc, np.ndarray):
   2923                 raise KeyError("Cannot get %s slice bound for non-unique "
-> 2924                                "label: %r" % (side, original_label))
   2925 
   2926         if isinstance(slc, slice):

KeyError: "Cannot get right slice bound for non-unique label: 'c'"
What is going on here is the values in 'id' column are non-unique. To enable detection of this condition the attribute .index.is_montonic_increasing and .index_montonic_decreasing return a boolean to test for this non-uniqueness property.

Applied to the df5 DataFrame created in cell #54 above returns False.

In [59]:
df5.index.is_monotonic_increasing
Out[59]:
False
The .is_monotonic_increasing attribute applied to the first DataFrame created above, 'df' returns true.

In [60]:
df.index.is_monotonic_increasing
Out[60]:
True
While not spelled out in any documentation I found, the moral of the story is when using indices with non-unique values, be wary.

## Resources

Indexing and Selecting Data pandas 0.19.0 doucmentation

Selecting DataFrame rows and columns using iloc, loc, and ix in Pandas by Shane Lynn

Indexing, Slicing and Subsetting DataFrames in Python by Chris Friedline.

Non-monotonic indexes require exact matches pandas 0.19.0 documentation

Useful pandas Snippets by Becky Swegler, Computers are for People.

## Navigation

Return to Chapter List