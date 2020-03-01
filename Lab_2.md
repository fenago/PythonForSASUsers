<img align="right" src="../logo-small.png">

# Lab : 

#### Pre-reqs:
- Google Chrome (Recommended)

#### Lab Environment
Notebooks are ready to run. All packages have been installed. There is no requirement for any setup.

**Note:** Elev8ed Notebooks (powered by Jupyter) will be accessible at the port given to you by your instructor. Password for jupyterLab : `1234`

All Notebooks are present in `work/PythonForSASUsers` folder. To copy and paste: use **Control-C** and to paste inside of a terminal, use **Control-V**

You can access jupyter lab at `<host-ip>:<port>/lab/workspaces/lab`


## Chapter 02 -- Data Structures

## Topics Covered:

list

indexing

tuple

dictionary

sequences

set

Resources

This chapter briefly touches on Python's data structures. As the name suggests, data structures are containers for data and other objects. We introduce these objects since they form the building-blocks for other structures discussed in subsequent chapters.

A cursory examination is needed, so a quick review is in order. Later, you will want to refer back to these since they aid the creation of DataFrames and other useful objects. DataFrames are built on top of Python and Numpy modules which are the predominate structures discussed in Chapter 4 onwards.

Python has four main data structures. There are:

1. list
2. tuple
3. set
4. dictionary

## list
A list contains an ordered collection of items. You determine the order. In Python, items in a list are separarated by commas and enclosed in square brackets. Lists are mutable, meaning you can add, remove, or alter items.

In [1]:
a_list = ['ale', 'lager', 'stout', 'hefeweizen', 'stout']
print('There are', len(a_list), 'items in "a_list"')
There are 5 items in "a_list"
In the example below, the parameter:

end=' '

for the print() method suppresses the default new-line character (CR/LF).

In [2]:
print("A list of beers include:")
for i in a_list:
    print(i, end=' ')
A list of beers include:
ale lager stout hefeweizen stout 

## Indexing
Python provides indexing methods for a number of objects, including lists. SAS has a similiar construct _n_ for the Data Step. Both indexing methods act as keys providing access to an individual item (or set of items). In Python's case, the default start index position is 0, and for SAS it is 1.

The SAS code example in the cell below is an imperfect analogy for a Python list, since the SAS logic uses a variable to hold the beer_type values. Nonetheless, each program illustrates access to an array 'item' by indexing.

Chapter 05 -- Understanding Indexes" has more details.

Python uses zero as its index start position, in contrast to SAS' _n_ which uses a start position of 1. Almost every example of SAS DO loops, by convention, use a start position of 1.

In [3]:
print('Value for beer type is:', a_list[0])
Value for beer type is: ale
    /******************************************************/
    /* c02_retrieve_list_item_by_index_position.sas       */
    /******************************************************/
    4         data beers;
    5         length beer_type $ 10;
    6         input beer_type $ @@;
    7         
    8         if _n_ = 1 then
    9            put 'Value for beer type is: ' beer_type;
    10        
    11        list;
    12        datalines;

    Value for beer type is: ale
    RULE:     ----+----1----+----2----+----3----+----4----+----5----+----6----+----7----+----8----+-
    13        ale lager stout hefeweizen stout
The next two examples illustrate the .append() and .sort() attributes for a list. They produce no visible output, so use the print() method to view results.

In [4]:
a_list.append('malt')
In [5]:
a_list.sort(reverse=True)
A for statement used to interate along the items in the list. For statements are documented here.

In [6]:
print("A list of beers include:")
for i in a_list:
    print(i, end=' ')
A list of beers include:
stout stout malt lager hefeweizen ale 
The .count() attribute returns the number of items in an object. Here it is used to return the number of occurrences of a value by having the list element as an argument.

In [7]:
print('a_list count for stout is:', a_list.count('stout'))
a_list count for stout is: 2
The example below illustrates Python's flexibility. Since nearly everything in Python is an object, you can have a list containing other lists. The built-in len() method provides a method to count the number of items in a list.

In [8]:
b_list = ['ales', 23, a_list]
print(b_list)
print('Item count for b_list is:', len(b_list))
['ales', 23, ['stout', 'stout', 'malt', 'lager', 'hefeweizen', 'ale']]
Item count for b_list is: 3

## tuple
A tuple is similar to a list, but unlike lists, are immutable. Tuples are defined by a list of comma-separated items inside a set of optional parentheses. Legibility of code demands their use, however.

A common use case for tuples is where Python statements or user-defined functions can assume that the items will not change, for example the names of the months.

In [9]:
dishes = ('eggs', 'green ham', 'biscuits', 'grits', 'steak')
print('The breakfast menu has:', len(dishes), 'items')
The breakfast menu has: 5 items
In [10]:
more_dishes = ('pancakes', 'cupcakes', 'twinkies', 'dishes')
for i in more_dishes:
    print(i, end=' ')
pancakes cupcakes twinkies dishes 
The tuple within a tuple remains one that is indexed

In [11]:
len(more_dishes)
Out[11]:
4

How to count all of the items in the tuple.

1. get the number of items in the tuple: more_dishes.  
2. subtract 1 since the 'tuple-within-a-tuple' item is not counted. 
3. add the length of the tuple more_dishes starting at position [3]

In [12]:
print('The number of items in more_dishes is:', len(more_dishes)-1+len(more_dishes[3]))
The number of items in more_dishes is: 9

## dictionary
A dictionary provides a look-up method through key/value pairs. Keys must be unique. Keys must be immutable objects such as lists, however, values can be either mutable or immutable objects.

Key/value pairs are specified as:

x = {key1: value1, key2: value2, key_n: value_n}

Create a dictionary

In [13]:
capital = {'Oregon' : 'Salem',
           'Washington' : 'Olympia',
           'California' : 'Sacrament',
           'Nevada' : 'Carson City'
          }
print(type(capital))
<class 'dict'>
Print a dictionary value by key

In [14]:
print('The capital of Nevada is', capital['Nevada'])
The capital of Nevada is Carson City
Add a key/value pair to the dictionary

In [15]:
capital['Colorado'] = 'Denver'
Printing key/value pairs. String formatting is covered in Chapter 03 -- Data Types and Formatting

In [16]:
print('Number of value/pairs in the dictionary capital is {}:'.format(len(capital)))
Number of value/pairs in the dictionary capital is 5:
Delete a value.

In [17]:
del capital['California']
Many Python operations are silent unless an error is raised. Check the length of the dictionary following the delete operation.

In [18]:
print('Number of value/pairs in the dictionary capital is {}:'.format(len(capital)))
Number of value/pairs in the dictionary capital is 4:

## sequences
There are three basic sequence types:

list 
tuple  
range

A feature for sequences is membership testing. Using operators such as in, not in, and concatenation, you can test for the presence or absences of values.

Implicit in these examples are boolean values(True False) being returned. Boolean operators are discussed in Chapter 03, Boolean operators.

The precedence order for sequence operations is found here.

The example below does an item membership test for the a_list list object.

In [19]:
item = 'ale'
if (item in a_list):
    print('found')
else:
    print('not found')
found
Item membership test in a tuple using the boolean and operator.

In [20]:
item1 = 'eggs' 
item2 = 'cupcakes'
if (item1 and item2 in more_dishes):
    print('found')
else:
    print('not found')
found
A for statement to iterate over the key value pairs in the 'capital' dictionary.

In [21]:
for state, capital in capital.items():
    print('The capital of {} is {}'.format(state, capital))
The capital of Washington is Olympia
The capital of Nevada is Carson City
The capital of Colorado is Denver
The capital of Oregon is Salem
if logic used for membership testing. The if/elif statements are documented here.

In [22]:
if 'Nevada' in capital:
    print("Nevada's capital:", capital['Nevada'])
Using the boolean not operator for membership test

In [23]:
'California' not in capital
Out[23]:
True

## set
A set object is an unordered collection of distinct objects. They are used for membership testing, removing duplicates from a sequence, and computing mathematical operations such as intersection, union, difference, and symmetric differences.

Another common use case is when the existence of an object is more important than order or obtaining a count of occurrences of items.

Membership test using the in operator for a set

In [24]:
months1 = set(['January', 'February', 'March', 'April', 'May', 'June'])

'Jan' in months1
Out[24]:
False
copy() method for sets

In [25]:
months2 = months1.copy()
The remove() method for sets to delete an item

In [26]:
months1.remove('February')
The add() method for adding an item to a set

In [27]:
months2.add('July')
The three examples above do not produce any visible output. The sets Month1 and Month2 are displayed below.

In [28]:
print('The set months1 contains:', months1)
print('The set months2 contains:', months2)
The set months1 contains: {'January', 'June', 'May', 'April', 'March'}
The set months2 contains: {'June', 'March', 'May', 'April', 'July', 'January', 'February'}
Now we can find the intersection of the sets 'months1' and 'months2'

In [29]:
months1 & months2
Out[29]:
{'April', 'January', 'June', 'March', 'May'}
Test if the set months2 is a super-set of the set months1

In [30]:
months2.issuperset(months1)
Out[30]:
True

## Resources:
v3.1.5 Documentation for Data Structures

Python for Data Analysis The landscape of tutorials describing a range of resources across the web. While a bit dated, it is useful for getting started and becoming familiar with the wide variety of packages built on top of Python.

A Byte of Python ...is a free book on programming using the Python language. It serves as a tutorial or guide to the Python language for a beginner audience. If all you know about computers is how to save text files, then this is the book for you.

Python Numpy Tutorial An overview of Nympy by Justin Johnson.

## Navigation
Return to Chapter List