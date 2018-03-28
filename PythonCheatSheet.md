# READ/WRITE TEXT FILES
* read txt method 1
```python
with codecs.open('filename', 'r', encoding = 'utf-8') as f:
    content = f.readlines() # content is a list. '\n' at the end of every line.
```

* read txt without linebreaks
```python
content = []
with open('filename', 'r') as f:
    for line in f:
        content.append(line.rstrip())
print(content)
```

* write to a txt, make sure there is a line break at the end of each line.
```python
out.write('\n'.join(str(i) for i in finalout)) # finalout is a list of strings
# in fact, join has to be applied to a list, # but we need to make sure that the no list is nested within the list finalout.
```

* to check if a list is empty:
```python
if lst:
    print list # this will print lst whenever it's NOT empty
```

* skip 10 iterations: e.g. the iterator is range(0,40), I want to skip range(21,30):
```python
# the best way to do it is use while:
while i < 41:
    do stuff
    if i = 21:
        then i = 30 # now we skip (21,30)
```

# TypeError: a bytes-like object is required, not 'str'
Two ways to solve it:
either use .decode() to the byte object, thus converting it to a string,
or use b'mystring' to make the string a byte object.

# print python path
```python
import sys 
print '\n'.join(sys.path)
```

# reverse a list:
```python
lst = ['john','mary','george','tom']
lst.reverse() # one way to reverse, destructively, like .sort()
lst[::-1] # non-destructively
```

# PANDAS DATAFRAME:

* df creation 1
```python
import pandas as pd
df = pd.DataFrame({'word': lista, 'freq': listb}) # now we have two columns 'word' and 'freq'
```

* df creation 2: using numpy
```python
import numpy as ny
df = df.DataFrame(np.zeros((30,5)), index=list(range(0,30)), columns=['a','b','c','d','e']) # or columns=list('abcde')

```

* read from a csv file, specifying the index col
```python
df = read_csv('test.csv', sep='\t', encoding='utf8', index_col=0) # or index=False. In this way, there will be no Unnamed:0 col.
```

* manipulating dfs
```python
df['word'] # get column
df.word # get col
df.ix[3] # ix = index, this shows the fourth row
##
df['word'] = 'new' # change the whole col to 'new'
del df['freq'] # delete the col 'freq'
df.index.name = 'No.' # assign a name 'No.' to the index col
df.index = list(range(1,101)) # make index start from 1
```

* assigning values to cells
First, it might be a good idea to specify data types in a df/col:
Two ways to do this:
1. ```df = pd.read_csv('./all.csv', index_col=0, encoding='gb18030', dtype={'INDEX': str}) # column INDEX should be of type string```
2. specify it in the ndarray if the df comes from an array:
```row = pd.DataFrame(np.full((1, 5), 'nan', dtype=str),                                index=list(range(0, 1)),                                columns=list('ABCDE'))```

Then there are many ways of assigning values:
```
.loc[] needs lables;
.iloc[] needs indices;
.ix[] supports mixed integer and label based access

But .at[] can be used for scalars. It is much faster than .loc family.
# sidenote: scalars are
# In [12]: np.ScalarType
# Out[13]:
# (int,  float,  complex,  long,  bool,  str,  unicode,  buffer,  numpy.int16,  numpy.float16,  numpy.int8,  numpy.uint64,  numpy.complex192,  numpy.void,
# numpy.uint32,  numpy.complex128,  numpy.unicode_,  numpy.uint32,  numpy.complex64,  numpy.string_,  
# numpy.uint16,  numpy.timedelta64,  numpy.bool_,  numpy.uint8,  numpy.datetime64,  numpy.object_,  numpy.int64,  numpy.float96,  numpy.int32,  numpy.float64,  numpy.int32,  numpy.float32)

Fastest way: see [here](http://stackoverflow.com/questions/13842088/set-value-for-particular-cell-in-pandas-dataframe)
df.set_value('rowlable', 'collable', valueYouWantToSet) # here the value better be of the same type as the column datatype specified before.
```

* selection of rows according to multiple criteria: [this page](http://stackoverflow.com/questions/22485375/efficiently-select-rows-that-match-one-of-several-values-in-pandas-dataframe)

If we have a df like this:
```
Name     Amount
---------------
Alice       100
Bob          50
Charlie     200
Alice        30
Charlie      10
```

Now ```df[(df.Name.isin(['Alice', 'Bob'])) & (df['Amount'] > 80)]``` will show  only the line of Alice


* append to a pd Series:
You can use the append function to add another element to it. Only, make a series of the new element, before you append it:
```python
test = test.append(pd.Series(200, index=[101]))
```
* tuples can NOT be index of a df! see [here](https://github.com/pydata/pandas/issues/7548). HOWEVER, there is a workaround:
```python
df.iloc[df.index.values.tolist().index(YourTuple)]
```

* one col divided by another: astype('folat')
```python
probDict['prob'] = probDict['C(Wn-1+Wn)']/probDict['C(Wn-1)'].astype('float')
```

## change data types of a column
[link](https://stackoverflow.com/questions/15891038/change-data-type-of-columns-in-pandas)
```python
df.columnName.astype('int32) # or float
# OR to float
pd.to_numeric(columnName)
```

## pandas documentation:
* [indexing and selecting data](http://pandas.pydata.org/pandas-docs/stable/indexing.html)

```python
# loc
In [45]: df1.loc[['a', 'b', 'd'], :]
Out[45]:
          A         B         C         D
a  0.132003 -0.827317 -0.076467 -1.187678
b  1.130127 -1.436737 -1.413681  1.607920
d  0.974466 -2.006747 -0.410001 -0.078638
#
# iloc
In [60]: df1.iloc[1:5, 2:4]
Out[60]:
          4         6
2  0.301624 -2.179861
4  1.462696 -1.743161
6  1.314232  0.690579
8  0.014871  3.357427
#
#
```

For getting a value explicitly (equiv to deprecated df.get_value('a','A'))

```python
# this is also equivalent to ``df1.at['a','A']``
In [50]: df1.loc['a', 'A']
Out[50]: 0.13200317033032932
```

* Fast scalar value getting and setting
Since indexing with [] must handle a lot of cases (single-label access, slicing, boolean indexing, etc.), it has a bit of overhead in order to figure out what you’re asking for. If you only want to access a scalar value, the fastest way is to use the at and iat methods, which are implemented on all of the data structures.

Similarly to loc, at provides label based scalar lookups, while, iat provides integer based lookups analogously to iloc
```python
In [117]: s.iat[5]
Out[117]: 5

In [118]: df.at[dates[5], 'A']
Out[118]: 0.1136484096888855

In [119]: df.iat[3, 0]
Out[119]: -0.70677113363008448
```
Fill the df with zeros:
```python
df[df!=0] = 0
```

May be useful:
http://stackoverflow.com/questions/17071871/select-rows-from-a-dataframe-based-on-values-in-a-column-in-pandas

sum rows of a df:
```python
dict( df[stance].sum(axis = 0) ) # sum the rows
```

change column names:
```python
df.columns = ['a', 'b']
```

select rows according criteria:
```python
#To select rows whose column value equals a scalar, some_value, use ==:
df.loc[df['column_name'] == some_value]

#To select rows whose column value is in an iterable, some_values, use isin:
df.loc[df['column_name'].isin(some_values)]

#To select rows whose column value does not equal some_value, use !=:
df.loc[df['column_name'] != some_value]

#isin returns a boolean Series, so to select rows whose value is not in some_values, negate the boolean Series using ~:
df.loc[~df['column_name'].isin(some_values)]

# select rows that do not contain a string::
~df.col3.str.contains('abc')

```

Multiple criteria:
http://stackoverflow.com/questions/15315452/selecting-with-complex-criteria-from-pandas-dataframe

Select rows according to criteria and then change the value of certain column:
```python
# change 2 to m of col 'gender'
df.loc[df['gender'] == 2, 'gender'] = 'm'
```

split dataframe to train and test
```python
import pandas as pd
import numpy as np
from sklearn.model_selection import train_test_split

train, test = train_test_split(df, test_size = 0.2)
```

filter string data based on its string length

```python
df = pd.read_csv('filex.csv')
df['A'] = df['A'].astype('str')
df['B'] = df['B'].astype('str')
mask = (df['A'].str.len() == 10) & (df['B'].str.len() == 10)
df = df.loc[mask]
print(df)

```

Get first 2 letters of a string from column
```python
dfout['name'] = dfout.['name'].str[0:2]
```

delete a column in pandas.
```python
# The best way to do this in pandas is to use drop:

df = df.drop('column_name', 1)
# where 1 is the axis number (0 for rows and 1 for columns.)

#To delete the column without having to reassign df you can do:

df.drop('column_name', axis=1, inplace=True)
#Finally, to drop by column number instead of by column label, try this to delete, e.g. the 1st, 2nd and 4th columns:

df.drop(df.columns[[0, 1, 3]], axis=1)  # df.columns is zero-based pd.Index
```
index name:
```python
#You can just get/set the index via its name property
df.index.name = 'foo'
```

## change the name of one colunm
```python
df=df.rename(columns = {'old_name':'new_name'})
```

## set one column to be index
```python
df.set_index('name_of_the_column', inplace=True)
```

## get levels of a column
```python
df.index.get_level_values('a').values
```

## change data types in pandas
```python
df[['two', 'three']] = df[['two', 'three']].astype(float)
```

## while else:

The else clause is only executed when your while condition becomes false. If you break out of the loop, or if an exception is raised, it won't be executed.
```python

# One way to think about it is as an if/else construct with respect to the condition:

if condition:
    handle_true()
else:
    handle_false()

# is analogous to the looping construct:

while condition:
    handle_true()
else:
    # condition is false now, handle and go on with the rest of the program
    handle_false()
```

# WORKING with json
* read in json file
```python
import json
# json.load takes in a file and parse it into json structure:
with open('filename.json', 'r') as handle:
    parsed = json.load(handle)
```

* json.loads takes in a string and parse it into json structure:
```python
# pretty print:
your_json = '["foo", {"bar":["baz", null, 1.0, 2]}]'
# we have to load the string as a json file, otherwise no pretty print is possible
parsed = json.loads(your_json)
# pretty print
print json.dumps(parsed, indent=4, sort_keys=True)
[
    "foo",     {
        "bar": [
            "baz",             null,             1.0,             2
        ]
    }
]
# on the other hand, json.dump/json.dumps read in a json structured file/string and
```

* convert it to a file/string
```python
# json.dump converts a json object to a file:
with open('file.json','w') as fh:
    json.dump(jsonobject, fh)
##
# json.dumps converts a json object to a string
a = {'foo': 3}
json.dumps(a)
'{"foo": 3}'
```

* some descriptions:

dumps can be used to take a Python data structure and generate a string in JSON format. That string can then be saved in a file, inserted in a database or sent over the wire.

Note that dump and load convert between files and objects, while dumps and loads convert between strings and objects. You can think of the s functions as wrappers around the s-less functions

# OTHER STUFF
## IF statements
For 'if' statements, the following counts as False:
- False
- None
- 0
- [] (empty list)
- {} (empty dict)
- ’’ (empty string)
- () (empty tuple)

Other stuff counts as True!

## print
```python
print(str, end=" ") # the end will be a space rather than a new line.
```
## sort
nun-destructive: sorted(lst)
destructive: lst.sort()

## change file name
```python
os.rename(oldname, newname) # can be full name including the directory.
```

## remove all 'a' in list ['a','b','c','a','d','a']
```python
lst = ['a','b','c','a','d','a']
lst = [x for x in lst if x != 'a']
```

## make a flat list
```python
[item for sublist in l for item in sublist]
```
## sort dictionary by value
```python
sorted(d.items(), key=lambda x: x[1])
```
## merge dictionaries
```python
z = {**x, **y} # x, y are the dicts you want to merge
```

## remove a key from a python dictionary
my_dict.pop('key', None)

## check if a string is the same as any string in a list of strings
```python
lst = ['a','b','c','d']
string = 'c'

any(string == x for x in lst) # or any(string in x for x in lst) to check substring relation

```

## check if a string starts with a digit
```python 
string[0].isdigit()
```

## pip, version control, etc.

### ImportError: numpy.core.multiarray failed to import

I removed the older version of numpy on my system using rm.

```python
import numpy as np
np.__path__
['/System/Library/Frameworks/Python.framework/Versions/2.7/Extras/lib/python/numpy']
exit()
```

sudo rm -rf /System/Library/Frameworks/Python.framework/Versions/2.7/Extras/lib/python/numpy

After this again tested:

```python
import numpy as np
np.__path__
['/usr/local/lib/python2.7/site-packages/numpy']
np.version
'1.10.1'
import tensorflow as tf
```

No issue after removing older version

## check where a package is installed
```python
import random
random.__file__
```
## regex sub (replace)
```python
pat = re.compile('(\((?P<tag>[A-z|\-]*?)\()')  # group: tag
m = pat.findall(t)
if m:
    print('\nfound (TAG(TAG! lacking a space!')
    print([x[0] for x in m])
    print('BEFORE:', t)
    t = pat.sub(r'(\g<tag> (', t)  # group: tag

    print('AFTER :', t)
```

## pandas change column order
```python
df = df[['mean', '0', '1', '2', '3']]
```

## default to empty list when building a dictionary
```python
# initialized the dict as a dict of lists
from collections import defaultdict 
dct = defaultdict(list)
# or:
dct = {}
dct.setdefault('foo', []).append('bar')

```

## regex: only search for whitespaces and tags, not newlines
```python
outstr = re.sub(u'[ \t\u3000]+', '', outstr) # \u3000 is the Chinese space
```

## remove sklearn warning
```python
# suppress sklearn warning
def warn(*args, **kwargs):
    pass
import warnings
warnings.warn = warn
```

## get time, date
```python
import time 
print (time.strftime("%H:%M:%S")) # 23:46:08
print (time.strftime("%d/%m/%Y")) # 04/10/2013
```

## log to a file and console at the same time
```python
import logging
import time

numEpoch=20

log = logging.getLogger()
log.setLevel(logging.INFO)

formatter = logging.Formatter('%(asctime)s - %(name)s - %(levelname)s - %(message)s')

fh = logging.FileHandler(time.strftime("%H:%M:%S")+'-epoch-'+str(numEpoch)+'.log')
fh.setFormatter(formatter)

ch = logging.StreamHandler(sys.stdout)
ch.setLevel(logging.INFO)
ch.setFormatter(formatter)

log.addHandler(ch)
log.addHandler(fh)
```

# install virtual env w/o ROOT:
Perhaps this was valid for older versions of virtualenv. For now, if you want to run virtualenv on a machine you're not root: download and untar one if these: https://github.com/pypa/virtualenv/releases, then then just run python virtualenv.py /path/to/new/virtualenv/to/create
[link](https://stackoverflow.com/questions/9348869/how-to-install-virtualenv-without-using-sudo)
