<img align="right" src="./logo-small.png">

# Lab : Panda Time Series and Date Handling

#### Pre-reqs:
- Google Chrome (Recommended)

#### Lab Environment
Notebooks are ready to run. All packages have been installed. There is no requirement for any setup.

**Note:** Elev8ed Notebooks (powered by Jupyter) will be accessible at the port given to you by your instructor. Password for jupyterLab : `1234`

All Notebooks are present in `work/PythonForSASUsers` folder. To copy and paste: use **Control-C** and to paste inside of a terminal, use **Control-V**

You can access jupyter lab at `<host-ip>:<port>/lab/workspaces/lab10_TimeSeries`

##### Run Notebook
Click notebook `Chapter 09 -- Panda Time Series and Date Handling.ipynb` in jupterLab UI and run jupyter notebook.

## Topics Covered:

Creating and manipulating a fixed-frequency of dates and time spans

Time Series Walk-Through

Returning Unique Levels of Categories

Return a Row using a Minimum Value

Return a Row using a Maximum Value

Convert time series from one frequency to another

Plotting with bokeh

Resources

Chapter 8, Understanding Date Time and TimeDelta objects provided a short introduction to Python's built-in datetime capabilities. In this chapter we illustrate pandas time series and date handling.

```
In [1]:
from datetime import date, time, datetime, timedelta
import numpy as np
import pandas as pd
from pandas import Series, DataFrame, Index
from numpy.random import randn as rnd
from IPython.display import Image

## Creating and manipulating a fixed-frequency of dates and time spans

The pd.date_range() method generates a DateTime Index which is applied to a panda Series or DataFrame to provide datetime interval indexing. We will see examples of its construction methods. And later we will utilize indexers taking advange of the Date TimeIndex.

```
In [2]:
rng = pd.date_range('1/1/2016', periods=30, freq='D')
```
In [3]:
rng
```

Out[3]:
DatetimeIndex(['2016-01-01', '2016-01-02', '2016-01-03', '2016-01-04',
               '2016-01-05', '2016-01-06', '2016-01-07', '2016-01-08',
               '2016-01-09', '2016-01-10', '2016-01-11', '2016-01-12',
               '2016-01-13', '2016-01-14', '2016-01-15', '2016-01-16',
               '2016-01-17', '2016-01-18', '2016-01-19', '2016-01-20',
               '2016-01-21', '2016-01-22', '2016-01-23', '2016-01-24',
               '2016-01-25', '2016-01-26', '2016-01-27', '2016-01-28',
               '2016-01-29', '2016-01-30'],
              dtype='datetime64[ns]', freq='D')
Print the first 10 dates in the DateTimeIndex

```
In [4]:
rng[:10]
```

Out[4]:
DatetimeIndex(['2016-01-01', '2016-01-02', '2016-01-03', '2016-01-04',
               '2016-01-05', '2016-01-06', '2016-01-07', '2016-01-08',
               '2016-01-09', '2016-01-10'],
              dtype='datetime64[ns]', freq='D')
Assemble a Series by using strings and integers for columns. Map the year, month, and day value into a date timestamp using the pd.to_datetime() method. Details for the pd.datetime() method are found here.

```
In [5]:
df = pd.DataFrame({'year': ['2014', '2015', '2016'],
                   'month': [1, 2, 3],
                   'day': [1,2,3,]})
df1 = pd.to_datetime(df)
df1
```

Out[5]:
0   2014-01-01
1   2015-02-02
2   2016-03-03
dtype: datetime64[ns]
Construct the Series 'b_rng' containing only business days using the pd.bdate() method and supplying start and end date.

```
In [6]:
start = datetime(2016, 1, 1)
end = datetime(2016, 12, 31)
b_rng = pd.bdate_range(start,end)
The Series implicitly creates a DatetimeIndex object.

```
In [7]:
type(b_rng)
```

Out[7]:
pandas.tseries.index.DatetimeIndex
```
In [8]:
b_rng
```

Out[8]:
DatetimeIndex(['2016-01-01', '2016-01-04', '2016-01-05', '2016-01-06',
               '2016-01-07', '2016-01-08', '2016-01-11', '2016-01-12',
               '2016-01-13', '2016-01-14',
               ...
               '2016-12-19', '2016-12-20', '2016-12-21', '2016-12-22',
               '2016-12-23', '2016-12-26', '2016-12-27', '2016-12-28',
               '2016-12-29', '2016-12-30'],
              dtype='datetime64[ns]', length=261, freq='B')
Create a Series containing the last business day of the month for 2016.

```
In [9]:
rng = pd.date_range(start, end, freq='BM')
ts = pd.Series(np.random.randn(len(rng)), index=rng)
ts.index
```

Out[9]:
DatetimeIndex(['2016-01-29', '2016-02-29', '2016-03-31', '2016-04-29',
               '2016-05-31', '2016-06-30', '2016-07-29', '2016-08-31',
               '2016-09-30', '2016-10-31', '2016-11-30', '2016-12-30'],
              dtype='datetime64[ns]', freq='BM')
Returns the first 5 last business day of the month for 2016.

```
In [10]:
ts[:5].index
```

Out[10]:
DatetimeIndex(['2016-01-29', '2016-02-29', '2016-03-31', '2016-04-29',
               '2016-05-31'],
              dtype='datetime64[ns]', freq='BM')
Returns the last business day of every other month in 2016.

```
In [11]:
ts[::2]
```

Out[11]:
2016-01-29    1.246634
2016-03-31    0.702623
2016-05-31    0.249486
2016-07-29    0.795634
2016-09-30    1.502413
2016-11-30   -0.820219
Freq: 2BM, dtype: float64

## Time Series Walk-Through

We can begin combining features covered in previous chapters to conduct a walk-through of a simple time series analysis.

The Data
The data is the FHFA House Price Index (HPI) which is a broad measure of the movement of single-family house prices. It is a weighted, repeat-sales index, meaning that it measures average price changes in repeat sales or refinancing on the same properties. This information is obtained by reviewing repeat mortgage transactions on single-family properties whose mortgages have been purchased or securitized by Fannie Mae or Freddie Mac.

Details about the data and how it is organized can be found here . This time series begins January 1991 and end August 2016. Both the seasonally adjusted index 'index_sa' and the non-seaonally adjusted index 'index_nsa' set the index value at 100 for January 1991.

**The Inquiry**

The three salient questions to answer are:

1. Have U.S. aggregate home prices recovered their value since the Great Recession of 2008-2010? 
2. Where are the highest and lowest values for 'traditional', 'purchase-only' homes in the U.S. in 2016? 
3. How do the highest and lowest home values market segments compare to the aggregate U.S. home prices?

**The Approach**

The input .csv file located here .

The file is composed of two parts. Part 1, rows 2 to 3081 are records for the aggregate market groups at the Census Division level. The frequency interval is monthly.

This portion of the file has 10 columns containing values for major market segments and the U.S. aggregate prices. The price indicies are both seasonally adjusted and non-seasonally adjusted values. The end-result DataFrame will be called 'df_us'.

Part 2, rows 3082 to 96,243 are more granular with a quarterly frequency interval. The major U.S. market segments are broken into smaller geographics areas with just the non-seasonaly adjusted home index value. Accordingly, the 10th column 'index_sa' contains no values at this location to the end of the file. The end-result DataFrame will be called 'df_states'.

Part 1 of .csv File to Construct the 'df_us' DataFrame
Start with a test read of the entire .csv file. The pd.read_csv method has the one required arguement, the input file name to create the DataFrame 'df_all'.

```
In [12]:
df_all = pd.read_csv("data/HPI_master.csv")
Inspect the first 5 rows to determine if the read_csv() method is giving the expected results.

```
In [13]:
df_all.head()

![](./images_9/1.jpg)

We need to combine the year and period fields into a datetime timetamp . The .csv file in the cell above is read without any datetime parsing for the fields, 'yr' and 'period'. We could post-process these fields to construct the appropriate date timestamp values.

A better approach is below. The parse_dates= argument allows a dictionary object with the key being the arbitrary name of the new column created and the key values indicating which fields are to be read in the .csv file. Recall that Python indexes have a start position of 0. In the .csv file, these fields are the 7th and 8th column position.

Sometimes, you may need to create your own date-parser, analogous to building a user-defined SAS INFORMAT to map field values into a datetime object. This is particularly true in cases where the date value is stored as component values in multiple fields.

The nrows= argument value is set to 3080.

```
In [14]:
df_us = pd.read_csv("data/HPI_master.csv",
                 parse_dates={'date_idx': [6,7]},
                 nrows=3080)
```
In [15]:
df_us.shape
```

Out[15]:
(3080, 9)
Validate column names and their data types. Confirm the date parser constructed the column 'date_idx' as a datetime object.

```
In [16]:
df_us.info()
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 3080 entries, 0 to 3079
Data columns (total 9 columns):
date_idx      3080 non-null datetime64[ns]
hpi_type      3080 non-null object
hpi_flavor    3080 non-null object
frequency     3080 non-null object
level         3080 non-null object
place_name    3080 non-null object
place_id      3080 non-null object
index_nsa     3080 non-null float64
index_sa      3080 non-null float64
dtypes: datetime64[ns](1), float64(2), object(6)
memory usage: 216.6+ KB
Check for missing values.

```
In [17]:
df_us.isnull().any()
```

Out[17]:
date_idx      False
hpi_type      False
hpi_flavor    False
frequency     False
level         False
place_name    False
place_id      False
index_nsa     False
index_sa      False
dtype: bool
Set the 'date_idx' column as the index on the DataFrame.

```
In [18]:
df_us.set_index("date_idx", inplace=True, drop=False)
Indexing on the datetime column 'date' creates a 'datetime-aware' DateTimeIndex.

```
In [19]:
df_us.index
```

Out[19]:
DatetimeIndex(['1991-01-01', '1991-02-01', '1991-03-01', '1991-04-01',
               '1991-05-01', '1991-06-01', '1991-07-01', '1991-08-01',
               '1991-09-01', '1991-10-01',
               ...
               '2015-11-01', '2015-12-01', '2016-01-01', '2016-02-01',
               '2016-03-01', '2016-04-01', '2016-05-01', '2016-06-01',
               '2016-07-01', '2016-08-01'],
              dtype='datetime64[ns]', name='date_idx', length=3080, freq=None)
Get the earlies and lastest date values in the 'df_us' DataFrame.

```
In [20]:
print('Earliest date is:', df_us.date_idx.min())
print('Latest date is:', df_us.date_idx.max())
Earliest date is: 1991-01-01 00:00:00
Latest date is: 2016-08-01 00:00:00
We see from the list of columns returned from the .info() attribute above we have several categorical columns. We need to understand their levels. Earlier, we saw the .describe() method used for numerical columns. In the example below specifying the 'include=' argument returns a description of string columns.

Recall that dtype 'O' (not zero) indicates string values for a Series or DataFrame column.

```
In [21]:
df_us.describe(include=['O'])
```

Out[21]:
hpi_type	hpi_flavor	frequency	level	place_name	place_id
count	3080	3080	3080	3080	3080	3080
unique	1	1	1	1	10	10
top	traditional	purchase-only	monthly	USA or Census Division	New England Division	DV_WNC
freq	3080	3080	3080	3080	308	308
The 'place_name' column has 10 unique levels or values. We can examine these values with the .unique() attribute.

```
In [22]:
df_us.place_name.unique()
```

Out[22]:
array(['East North Central Division', 'East South Central Division',
       'Middle Atlantic Division', 'Mountain Division',
       'New England Division', 'Pacific Division',
       'South Atlantic Division', 'West North Central Division',
       'West South Central Division', 'United States'], dtype=object)
Continue by setting an index on the column 'place_name'. Construct the 'df_us_plot' DataFrame with rows for 'place_name' equal to 'United States'. The .loc indexer allows row slicing which is covered in detail here.

```
In [23]:
df_us.set_index('place_name', inplace=True, drop=False)
df_us_plot = df_us.loc['United States']
Time series data lends itself well to plotting. The bokeh package is used to plot the non-seasonal home price index for the entire U.S. using the 'df_us_plot' DataFrame created above

```
In [24]:
import bokeh.charts
import bokeh.charts.utils
import bokeh.io
import bokeh.models
import bokeh.palettes
import bokeh.plotting

# Display graphics in this notebook
bokeh.io.output_notebook()
Loading BokehJS ...
Plot the monthly aggregate home index values for the U.S. using the earliest and latest dates from the 'df_us_plot' DataFrame.

```
In [25]:
p = bokeh.charts.Line(df_us_plot, x='date_idx', y='index_nsa', color='firebrick', 
                      title="Monthly Aggregate Home Price Values in the U.S.")

# Display it
bokeh.io.show(p)
Answer to question 1
Have U.S. aggregate home prices recovered their value since the Great Recession of 2008-2010?
During the Great Recession of 2008-2010, home prices across the U.S. declined dramatically. The aggregate U.S. home price index has recovered all of the losses since that time and have continued to experience steady growth.

Create the DataFrame 'df_us_3' to select the rows with the values indicated below.

```
In [26]:
df_us_3 = df_us.loc[['West South Central Division', 'United States', 'Pacific Division']]
```
In [27]:
df_us_3.info()
<class 'pandas.core.frame.DataFrame'>
Index: 924 entries, West South Central Division to Pacific Division
Data columns (total 9 columns):
date_idx      924 non-null datetime64[ns]
hpi_type      924 non-null object
hpi_flavor    924 non-null object
frequency     924 non-null object
level         924 non-null object
place_name    924 non-null object
place_id      924 non-null object
index_nsa     924 non-null float64
index_sa      924 non-null float64
dtypes: datetime64[ns](1), float64(2), object(6)
memory usage: 72.2+ KB
Plot the 3 regions using the U.S. home price index for comparison. Rather than specify a color value for color=, specifying a column label produces multiple plots based on the the levels.

```
In [28]:
p = bokeh.charts.Line(df_us_3, x='date_idx', y='index_nsa', color='place_name', 
                      title="Monthly Home Price in West South Central and Pacific Division compared to U.S.",
                      legend="top_left")

bokeh.io.show(p)
Part 2 of .csv File to Construct the 'df_states' DataFrame
Use to 'skiprows=' argument to begin reading at row 3082. We use a tuple to specify the columns labels.

Beginning with row 3082 the values for the field 'index_sa' are missing. Begin the read at row 3082 until end of file. And since the default is to key off column names, supply column labels with a<a href= "http://nbviewer.jupyter.org/github/RandyBetancourt/PythonForSASUsers/blob/master/Chapter%2002%20--%20Data%20Structures.ipynb#tuple"> tuple</a> of names. The usecols= argument uses a tuple of integers to indicate which fields are to be read from the .csv file. Header=None is to prevent the reader from building column names at row position nrows-1, which in our case contains data values.

```
In [29]:
df_states = pd.read_csv("data/HPI_master.csv",                 
            skiprows=3082,
            usecols=(0, 1, 2, 3, 4, 5, 6, 7, 8),
            names=('hpi_type', 'hpi_flavor', 'frequency', 'level', 'place_name', 'place_id', 'yr', 'period', 'index_nsa'),
            header=None)
The columns 'yr' and 'period' are read as string values and need to be converted to datetime values. The operation below creates the new column 'date_str' by:

1. Concatenating 'yr' with 'Q' with 'period' to form a date string 
2. The date string is passed to the pd.to_datetime function creating the dateime column 'date_idx'
1. String concatenation operation to form YYYYq.
```
In [30]:
df_states["date_str"] = df_states['yr'].map(str) + 'Q' + df_states['period'].map(str)
Display the 'date_str' value using the iloc indexer which returns row and column location by integer positions.

```
In [31]:
df_states.iloc[0,-1]
```

Out[31]:
'1986Q4'
2. Convert the 'date_str' column into a panda datetime column called 'date_idx'. 
```
In [32]:
df_states['date_idx'] = pd.to_datetime(df_states['date_str'])
Inspect the first 5 records in the 'df_states' DataFrame.

```
In [33]:
df_states.head()

![](./images_9/2.jpg)

Return the number of rows and columns in the DataFrame.

```
In [34]:
df_states.shape
```

Out[34]:
(96244, 11)
The following SAS Data Step reads the same .csv file using FIRSTOBS= to begin reading from the arbitary row position 3082.

    /********************************/
    /* c09_read()_csv_df_states.sas */
    /********************************/
    data df_states;
          infile 'C:\Data\HPI_master.csv' delimiter=',' missover dsd firstobs=3082; 
          informat hpi_type $12.
                   hpi_flavor $16.
                   frequency $9.
                   level $28.
                   place_name $33.
                   place_id $8.
                   yr $5.
                   period $6.
                   index_nsa 8.;
             input hpi_type $
                   hpi_flavor $
                   frequency $
                   level $
                   place_name $
                   place_id $
                   yr $
                   period $
                   index_nsa ;
Display the first 5 rows of the SAS data set df_states. In the SAS code example below the 'yr' and 'period' variables are combined to create the SAS datetime variable 'date_idx'.

```
In [35]:
Image(filename='output/df_states_output.JPG')

![](./images_9/3.jpg)

Inspect values for the categorical columns.

```
In [36]:
df_states.describe(include=['O'])

![](./images_9/4.jpg)

## Returning Unique Levels of Categories

Get unique values for column 'level'

```
In [37]:
df_states.level.unique()
```

Out[37]:
array(['MSA', 'State', 'USA or Census Division', 'Puerto Rico'], dtype=object)
PROC SQL used to obtain unique values from the variable 'level'.

    /******************************************************/
    /* c09_select_unique_level.sas                        */
    /******************************************************/
    56       proc sql;
    57          select unique level
    58          from df_states;
    59       quit;

```
In [38]:
Image(filename='output/level_unique_values.JPG')

![](./images_9/5.jpg)

Start by creating a list called 'lvls' using the .select_dtypes() attribute to include columns with dtype value 'O' selecting columns with string values.

```
In [39]:
lvls = list(df_states.select_dtypes(include=['O']).columns)
Inspect the 'lvls' list.

```
In [40]:
lvls
```

Out[40]:
['hpi_type',
 'hpi_flavor',
 'frequency',
 'level',
 'place_name',
 'place_id',
 'date_str']
If we had a long list of columns needing to determine unique values the approach above becomes tedious; an iterative approach is called for using the for statement shown below.

```
In [41]:
for item in lvls[:4]:
   print(item, 'levels are:', df_states[item].unique())
hpi_type levels are: ['traditional' 'non-metro' 'distress-free' 'developmental']
hpi_flavor levels are: ['all-transactions' 'expanded-data' 'purchase-only']
frequency levels are: ['quarterly']
level levels are: ['MSA' 'State' 'USA or Census Division' 'Puerto Rico']
Using the levels for the categorial columns displayed above, filter the 'df_state' DataFrame to match the 'df_us' Dataframe. For this row slicing operation, create a boolean mask and combine it with the .loc indexer described here. This is a common pattern for filtering rows or column values.

Create a boolean mask for the filtering criteria.

```
In [42]:
mask = (df_states['hpi_type'] == 'traditional') & (df_states['hpi_flavor'] == 'purchase-only') & \
       (df_states['level'] == 'State')
Apply the mask. Notice the 'df_states' DataFrame is updated in place with this assignment. The original 'df_states' DataFrame had 96244 rows.

```
In [43]:
df_states = df_states.loc[mask]
The .shape attribute returns the new row and column count for the 'df_states' DataFrame.

```
In [44]:
df_states.shape
```

Out[44]:
(5202, 11)

**Minimums and Maximums for Part 2, 'df_states' DataFrame**

Index the column 'index_nsa' in order to find the maximum and minimum for the 'df_states' DataFrame.

```
In [45]:
df_states.set_index('index_nsa', inplace=True, drop=False)
```
In [46]:
print('Max value for index_nsa:', df_states['index_nsa'].max())
print('Min value for index_nsa:', df_states['index_nsa'].min())
Max value for index_nsa: 503.11
Min value for index_nsa: 82.37
PROC SQL for finding min and max for the variable 'index_nsa'.

    /******************************************************/
    /* c09_min_max_index_nsa.sas                          */
    /******************************************************/
37      proc sql;
38         select max (index_nsa) as max_index_nsa,
39                min (index_nsa) as min_index_nsa
40         from df_states
41         where hpi_type ='traditional' and  hpi_flavor = 'purchase-only' and
42          level = 'State';
43      quit;
```
In [47]:
Image(filename='output/max_min_index_nsa.JPG')

![](./images_9/6.jpg)

Filter for 2016
For 2016, return the rows with the largest and smallest value for 'index_nsa'.

In order to find the row with the lowest 'index_nsa' value, the example below uses a multi-step process:

1. Create a boolean mask filtering the rows with 'date_idx' between January 1, 2016 and December 31, 2016
2. Use the .loc indexer applying the mask to create the 'df_2016' DataFrame
3. Use the .idxmin() attribute to return the row having the minimum 'index_nsa' value
4. Use the .idxmax() attribute to return the row having the maximum 'index_nsa' value

Keep in mind that the 'df_states' DataFrame has already been filtered previously.

Create the boolean mask for the filtering criteria.

```
In [48]:
mask1 = (df_states['date_idx'] >= '2016-01-01') & (df_states['date_idx'] <= '2016-12-31')
Apply the mask using the .loc indexer

```
In [49]:
df_2016 = df_states.loc[mask1]

## Return a Row using a Minimum Value

Use the .idxmin() method to return the minimum 'index_nsa' value.

```
In [50]:
df_2016.ix[df_2016['index_nsa'].idxmin()]
```

Out[50]:
hpi_type              traditional
hpi_flavor          purchase-only
frequency               quarterly
level                       State
place_name            Connecticut
place_id                       CT
yr                           2016
period                          1
index_nsa                  165.81
date_str                   2016Q1
date_idx      2016-01-01 00:00:00
Name: 165.81, dtype: object
Return a Row using a Maximum Value
Use the .idxmax() attribute to return the row with the maximum 'index_nsa' value.

```
In [51]:
df_2016.ix[df_2016['index_nsa'].idxmax()]
```

Out[51]:
hpi_type               traditional
hpi_flavor           purchase-only
frequency                quarterly
level                        State
place_name    District of Columbia
place_id                        DC
yr                            2016
period                           2
index_nsa                   501.35
date_str                    2016Q2
date_idx       2016-04-01 00:00:00
Name: 501.35, dtype: object
The .min and .max attribute return minimum and maximum respectively. The .idxmin() attribute returns the entire row as a Series. This enables the ability to use other column values, like 'place_name' or 'place_id' as further filtering and selection criteria.

The .min() and .max attributes return a scalar.

```
In [52]:
print("2016 minimum value for 'index_nsa':", df_2016.index_nsa.min())
print("2016 maximum value for 'index_nsa':", df_2016.index_nsa.max())
2016 minimum value for 'index_nsa': 165.81
2016 maximum value for 'index_nsa': 501.35
The SAS Data Step above used to the read .csv file does not create a SAS datetime variable. This is illustrated below.

The SAS example below uses a pair of PUT functions nested inside the YYQ function to create the SAS date variable 'date_idx'. The PUT functions map the variables 'yr' and 'period' from character to numeric. The YYQ function described here returns a SAS datetime values from year and quarter values.

The Data Step below is continued from the SAS Data Step example above used to read the .csv file.

    /******************************************************/
    /* c09_min_max_index_nsa_for2016.sas                  */
    /******************************************************/
    12      date_idx=yyq(put(yr,8.),put(period,8.));
    13      format date_idx yyq10.;
    14      
    15      proc sql;
    16         select max (index_nsa) as max_index_nsa,
    17                min (index_nsa) as min_index_nsa
    18         from df_states
    19         where hpi_type ='traditional' and
    20               hpi_flavor = 'purchase-only' and
    21               level = 'State' and
    22               date_idx between '01Jan2016'd and '31Dec2016'd;
    23      quit;
```
In [53]:
Image(filename='output/2016_min_max.JPG')

![](./images_9/7.jpg)

Answer to question #2
2. Where are the highest and lowest values for 'traditional', 'purchase-only' homes in the U.S. in 2016? 

Lowest home prices are in the state of Connecticut and the highest are in Washington, D.C.

Recall the 'df_states' DataFrame was filtered for column 'hpi_type' = 'traditional' and column 'hpi_flavor' = 'purchase-only' and level='state'. This DataFrame was subsequently filtered to create the DataFrame 'df_2016' in which we searched for the lowest and highest home index values.

## Convert Time Series from one Frequency to Another

In order to answer the last question: How do the highest and lowest home values market segments compare to the aggregate U.S. home prices?

Information from the 'df_states' and 'df_us' DataFrame need to be combined. The 'df_us' monthly values need to be downsampled to quarterly. In other words, aggregated to a lower frequency. In doing so, the frequency in both DataFrames become quarterly.

In order to answer the 3rd question above, we need to conduct the following:

1. Set the index for 'df_us' DataFrame to the column 'date_idx'
2. Resample the 'df_us' DataFrame aggregating from monthly to quarterly using mean values
3. Create a column in the 'df_us' labeled 'place_name' with the value "U.S. Aggregate".  
4. Extract rows from 'df_states' DataFrame for lowest and highest home values using the column 'place_name' 
5. Merge (concatenate) the resampled 'df_us' DataFrame with the high & low value rows from the extract operation
6. Plot the resulting 'DataFrame'
Start by examining the quarterly date values from the 'df_states' DataFrame...

```
In [54]:
df_states.iloc[0:4, -1]
```

Out[54]:
index_nsa
100.00   1991-01-01
100.72   1991-04-01
101.58   1991-07-01
102.48   1991-10-01
Name: date_idx, dtype: datetime64[ns]
...and compare with the monthly date values from the 'df_us' DataFrame.

```
In [55]:
df_us.iloc[0:4, 7]
```

Out[55]:
place_name
East North Central Division    100.00
East North Central Division    101.03
East North Central Division    101.40
East North Central Division    101.79
Name: index_nsa, dtype: float64
1. Set the index for 'df_us' DataFrame to the column 'date_idx'
```
In [56]:
df_us.set_index('date_idx', inplace=True, drop=False)
2. Resample the 'df_us' DataFrame aggregating from monthly to quarterly using mean values
pandas can perform resampling operations during frequency conversion (in this case, an aggregation of monthly values into quarterly values for the 'df_us' DataFrame). This is a common pattern in time series analysis.

The .resample() method is a time-based needs a link to groupby operation, followed by a reduction method on each of its groups. Frequency conversion doc is found here. The .resample() method accepts frequency offset suffixes that are listed here.

In order to align the monthly date values found in the 'df_us' DataFrame, with the quarterly date values in the 'df_states' DataFrame, use the 'QS' date offset. 'QS' sets the date value frequency to quarterly with year ending in December. The resulting DataFrame is called 'df_us_qtr'.

```
In [57]:
df_us_qtr = df_us.resample('QS').mean()
3. Create a column in the 'df_us' labeled 'place_name' with the value "U.S. Aggregate".
```
In [58]:
df_us_qtr['place_name'] = 'U.S. Aggregate'
4. Extract rows for the lowest and highest home values using the df_us_qtr['place_name'] column
Set the index to the column 'place_name' and extract the rows using the .loc indexer.

```
In [59]:
df_states.set_index('place_name', inplace=True, drop=False)
Create a boolean mask using the logical OR operator ( | ).

```
In [60]:
mask = (df_states['place_name'] == 'Connecticut') | (df_states['place_name'] == 'District of Columbia')
Apply the mask using the .loc indexer to create the 'hi_lo' DataFrame.

```
In [61]:
hi_lo = df_states.loc[mask]
Reset the index since the 'hi_lo' DataFrame will be concatenated (merged) with the resampled 'us_df' DataFrame.

```
In [62]:
hi_lo.reset_index(drop=True, inplace=True)
5. Merge (concatenate) the resampled 'df_us' DataFrame with the high & low value rows from the extract operation
Reset the index, since the 'df_us_qtr' DataFrame will be merged with the 'hi_lo' DataFrame created above.

```
In [63]:
df_us_qtr.reset_index(inplace=True)
Create a list of DataFrames to be included in the concatenation operation.

```
In [64]:
frames = [df_us_qtr, hi_lo]
Create the DataFrame 'plot_hi_low' concatenating values for the lowest home prices (Connecticut), with the highest ('District of Columbia') together with the U.S. aggregate. This is accomplished using the needs a link to concat operation.

```
In [65]:
plot_hi_low = pd.concat(frames)

## Plotting with bokeh

```
In [66]:
p = bokeh.charts.Line(plot_hi_low, x='date_idx', y='index_nsa',  color='place_name', 
                     title= "Comparison of lowest & highest home values with U.S. aggregate index value")

# Display it
bokeh.io.show(p)

**Answer to Question 3.**

3. How do the highest and lowest home values market segments compare to the aggregate U.S. home prices?

In the plot above, we see that the aggregate U.S. home price index regained its losses from the Great Recession (2008-2010) beginning around 2015. Since then it has seen steady growth and has exceeded the peak from the pre-recession values.

In contrast, the home value index for Connecticut is just now recovering its value lost during the Great Recession. However, it has not recovered its pre-recession value.

Since 2011, the aggregate U.S. home price index has been growing faster than that of Connecticut, but not even close to the growth rate for Washington, D.C.

Drop the values for 'District of Columbia' to compare the lowest home prices with the 'U.S. Aggregate' home price index. Notice the automatic re-scaling of the Y-axis.

```
In [67]:
mask = (plot_hi_low['place_name'] != 'District of Columbia')
```
In [68]:
plt_lo_us = plot_hi_low.loc[mask]
```
In [69]:
p = bokeh.charts.Line(plt_lo_us, x='date_idx', y='index_nsa', color='place_name',  
                      title="Comparison of lowest & U.S. Aggregate home values")

bokeh.io.show(p)

## Resources

pandas Time Series and Date functionality doc located here.

pandas datetime Indexing doc located here.

pandas cookbook for timeseries.

Chapter 10, Time Series, "Python for Data Analysis, by Wes McKinney, located here.

SAS 9.4 Language Reference: Concepts, 5th ed., Dates, Times, and Intervals

