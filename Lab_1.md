<img align="right" src="./logo-small.png">

# Lab : Introduction

#### Pre-reqs:
- Google Chrome (Recommended)

#### Lab Environment
Notebooks are ready to run. All packages have been installed. There is no requirement for any setup.

**Note:** Elev8ed Notebooks (powered by Jupyter) will be accessible at the port given to you by your instructor. Password for jupyterLab : `1234`

All Notebooks are present in `work/PythonForSASUsers` folder. To copy and paste: use **Control-C** and to paste inside of a terminal, use **Control-V**

You can access jupyter lab at `<host-ip>:<port>/lab/workspaces/lab2_Introduction`

##### Run Notebook
Click notebook `Chapter 01 -- Introduction.ipynb` in jupterLab UI and run jupyter notebook.

### Why Python?

There are plenty of substantive open source software projects out there for data scientists, so why choose Python? After all, there is R. R is a robust and well-supported language written initially by statistician for statisticians.

The view is not to promote one solution over the other. The goal is to illustrate how the addition of Python to a SAS user’s skill set can broaden ones range of capabilities. And besides, Bob Muenchen has already written, R for SAS and SPSS Users.

Python has its heritage in scientific and technical computing domains and it has a compact syntax. The latter making for a relatively easy language to learn while the former means it scales to offer good performance with massive data volumes. This is one of the reasons why Google uses it so extensively and has developed an outstanding tutorial for programmers. See Google's Python Class here.

### A quick-start

Another aspect both languages have in common is the wealth of information available on the web.

You would think having a plethora of content makes learning a new language a straightforward proposition. However, at times I experienced information overload. As I worked though examples, I was not sure until a good investment of time if what I was learning was applicable to my objectives.

Sure there is learning for leaning’s sake. But not every tutorial or text I read was fruitful, however, most were. It was only after some time into this endeavor that I realized I needed a specific context for ingesting new information.

Like most people, I want fast results. And like most SAS users, I have developed a mental model for data analysis focused on a series of iterable steps.

What I was lacking was someone to identify both the content to utilize as well as the order in which it should be consumed. I wanted to initially invest time in just those topics that I needed before getting on with the task of data analysis.

### The Python for SAS Users Approach

A philosophical word (or two) about the merits of Python and SAS as languages. From my perspective, it is simply a question of finding the right tool for the job. Both languages have advantages and disadvantages. And since they are programming languages, their designers had to make certain tradeoffs which can manifest themselves as features or quirks, depending on one’s perspective.

The goal is to provide a quick start for users already familiar with the SAS lanaguage and enable them to become familiar with Python. The choice of which tool to utilize typically comes down to a combination of what you as a user are familiar with and the context of the problem being solved.

The approach taken is to introduce a concept(s) in Python with a description of how the program works followed by a code cell for the Python program. This is often followed by an example program in the language of SAS to present a compare and constrast approach. Not every Python example has an analog SAS example.

The Python code examples will always be inside a code cell within this notebook. To make the examples easy to follow, where reasonable, I have written the SAS output to the log or captured output as .JPG files. All of the SAS code examples are located here in 7zip compressed format. You can download a copy of 7zip from here.

The SAS language programs were written and verified with Version: 3.2.2.0 of the WPS Workbench for Windows. World Programming System offers a SAS language interpreter and can be reached at: https://worldprogramming.com/us/.

This approach is illustrated by the next 3 cells below. The analog SAS program is called c1_Python_for_loop.sas

### Comparing Python and SAS Code Fragments

The list of numbers contained inside the square brackets [ ] make up the elements in a Python list. In Python, a list is a data structure that holds an arbitrary collection of items. is an integer used as the index for the for loop. holds the integer value from the arithmetic assignment of Finally, the method writes the output. The same program is written in SAS as shown below.

![](.\images_1\1.jpg)

### Python Terminology

Python permits an object-oriented programming model. SAS is a procedural programming language. These examples use a procedural programming model for Python given the goal is to map SAS programming constructs into Python.

This object-oriented programming model provides a number of classes with objects being instances of the class. The Python program in the cell below illustrates the int class (integers). Variable x is an instance (object) of the int class. You can execute help(int) to read more.

Objects are said to belong to a class. Variables that belong to a class or objects are strictly speaking, refered to as fields. Objects have capabilities belonging to the class and are called methods().

My early experiences was that the object types I created were not always obvious from the code context. I neeed to know what type of object was being created. The type() method returns the object's type as illustrated in the cell below.

Python has a number of built-in functions and types that are always available which are documented here. Later on, we will see how Python expands its capabilities through importing packages (libraries).

![](.\images_1\2.jpg)

### Object References

Consider the program in the cell below. Don't worry about the syntax for now. a_list is a Python list object. The a_list list is copied to another list object using the assignment:

b_list = a_list
It turns out that while a_list and b_list are equivalent, they both point to the same memory location. In other words, b_list refers to the object a_list and does not represent the object itself.

The program statement:

del a_list[0]
removes the first item from the a_list list object. When we print the list objects, you see how both have the first item removed. The effect is subtle, but not one you will likely encounter a great deal. But certainly a subtly to be aware of.

![](.\images_1\3.jpg)


### The Zen of Python, by Tim Peters

![](.\images_1\4.jpg)

The Zen of Python, by Tim Peters

Beautiful is better than ugly.
Explicit is better than implicit.
Simple is better than complex.
Complex is better than complicated.
Flat is better than nested.
Sparse is better than dense.
Readability counts.
Special cases aren't special enough to break the rules.
Although practicality beats purity.
Errors should never pass silently.
Unless explicitly silenced.
In the face of ambiguity, refuse the temptation to guess.
There should be one-- and preferably only one --obvious way to do it.
Although that way may not be obvious at first unless you're Dutch.
Now is better than never.
Although never is often better than *right* now.
If the implementation is hard to explain, it's a bad idea.
If the implementation is easy to explain, it may be a good idea.
Namespaces are one honking great idea -- let's do more of those!

### Ledgibility, Indentation, and Spelling Matter

To quote, Eric Raymond, "A language that makes it hard to write elegant code makes it hard to write good code." From his essay, entitled, "Why Python", located at: http://www.linuxjournal.com/article/3882

The Python program in the cell below is the same as the one 6 cells above, with one exception. The line after the for block is not indented. This results in the interpreter raising the error:

IndentationError: expected an indented block

Once you get over the shock of how Python imposes the indentation requirements, you will come to see how this is an important feature used to create legible and easy-to-understand code.

Notice also there appear to be no symbols used to end a program statement. The end-of-line character is used to end a Python statement. This also helps to enforce legibility by keeping each statement on a separate physical line.

Coincidently, like SAS, Python will also honor a semi-colon as an end of statement terminator. However, you rarely see this. That's because multiple statements on the same physical line is considered an affront to program legibility.

![](.\images_1\5.jpg)

### Line continuation symbol

Should you find you have a line of code that needs to extend past the physical line (i.e. wrap), then use the backslash (\). This causes the Python interpreter to ignore the physical end-of-line terminator on the current line and continuing scanning for the next end-of-line terminator.

![](.\images_1\6.jpg)

### Spelling

Of course, the incorrect spelling of keywords is a source of error. Unlike SAS, in Python, object names are case sensetive.

![](.\images_1\7.jpg)

Finally, a word about name choices. Names should be descriptive because more than likely you will be one who has to re-read and understand tomorrow the code you write today. As with any language, it is a good practice to avoid language keywords for object names.

### Navigation

Return to Chapter List