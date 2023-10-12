---
title: python 5 minute quickstart
date: 2023-07-22 09:25:00
categories:
  - Quantitative Trading
tags: Quantitative trading
  - Quantitative Trading
  - python
  - Getting Started
  - Quick Start
  - Basics
  - 5 minutes
description: 'This document is not suitable for those who do not have a basic knowledge of python, it is suitable for those who have a basic knowledge of the language or have used python for some time but some of the syntax is a bit fuzzy, you can use this document as a memo, you can always refer to some of the fuzzy or not too solid to memorize the place '
cover: https://s2.loli.net/2023/07/24/7YKgcPjuiUDeRoL.webp
---

## Introduction

This document is just a simple introduction to syntax stacking, not for those who have no basic knowledge of programming, but for those who have a basic knowledge of the language or have been using python for a while but some of the syntax is a bit vague, you can use this document as a reminder to refer to some of the ambiguities or not too solid in memory.

Similarly, this article is not going to spend time on installing the programming language environment or installing and setting up the editor, as you will be able to solve these problems by default, or have already done so.

This article is written in python3, most of the examples are available in python2, and the few statements that don't work are no longer supported by the language or have been changed to a more elegant way of writing them.

Let's get started.

## Annotations and printouts

```python
# Single-line comments

""" 
Multi-line strings can be wrapped in
    three quotes, but this can also be treated as a
    multi-line comment
"""
# print("hello world!") # => output hello world!
print("hello world!") # => output hello world! 
```

## Data types and operators

### I Number types and related operations

```python
# Number types
4 # => 4

# Simple arithmetic
1 + 2 # => 3
8 - 2 # => 6
10 * 3 # => 30
35 / 7 # => 5

# Division of integers is automatically rounded
5 / 3 # => 1

# To do exact division, we need to introduce floating point numbers
3.0 # Floating point numbers
13.0 / 4.0 # => 3.25 Much more precise.

# Parentheses have the highest priority
(1 + 4) * 2 # => 10
```

### II Boolean data types

```python
True
False

# Use not to fetch nots
not True # => False
not False # => True

# Equal
2 == 2 # => True
3 == 1 # => False

# Not equal
4 ! 4 ! == 4 # => False
5 ! = 6 # => True

# More comparison operators
1 < 12 # => True
1 > 9 # => False
4 <= 4 # => True
6 >= 6 # => True

# Comparison operations can be written concatenated!
2 < 3 < 4 # => True
3 < 4 < 3 # => False
```

## String types and operations

```python
# The string needs to be enclosed in " or '
"Hello world!"
'Hello world!'

# String is enclosed in a plus sign
"Hello " + "world!" # => "Hello world!"

# String can be treated as a list of characters
"Hello world!"[0] # => 'H'

# % can be used to format strings
"%s can be %s" % ("Hello", "world!")

# Strings can also be formatted using the format method.
# It is recommended to use this method
"{0} can be {1}".format("strings", "formatted")
# You can also replace numbers with variable names
"{name} wants to eat {food}".format(name="Bob", food="lasagna")

# The method now more commonly used with the f argument
name, food = "Bob", "lasagna"
f"{name} wants to eat {food}"

# None is an object
None # => None

# Don't use the equality `==` symbol to compare with None.
# Use `is`.
"etc" is None # => False
None is None # => True

# 'is' can be used to compare objects for equality
# This operator is not very useful for comparing raw data, but is essential for comparing objects.

# None, 0, and the empty string are counted as False
# Everything else is True
0 == False # => True
"" == False # => True
```

## Variables and collections

Collections are mainly lists, lists, dictionaries, dicts and tuples. As for arrays, which are used in machine learning, they can be ignored for the time being because they are basically pandas DataFarmes data structures in python quantization.

### I Variable

```python
# There is no need to declare a variable before assigning it a value.
some_var = 5 # It is generally recommended to use a combination of lowercase letters and underscores for variable names.
some_var # => 5

# An exception is thrown when accessing an unassigned variable.
# See the section on control flow to see how exceptions are handled.
some_other_var # throws NameError

# The if statement can be used as an expression
"yahoo!" if 3 > 2 else 2 # => "yahoo!"
```



### II List Related Operations

```python
# A list is used to hold sequences
li = []
# Lists can be initialized directly
other_li = [4, 5, 6]

# Add elements to the end of the list
li.append(1) # li is now [1]
li.append(2) # li now is [1, 2]
li.append(4) # li now is [1, 2, 4]
li.append(3) # li now is [1, 2, 4, 3]
# Remove the end of the list
li.pop() # => 3 li is now [1, 2, 4]
# Add it back in
li.append(3) # li is now [1, 2, 4, 3] again.

# Access the list as you would an array in any other language.
li[0] # => 1
# Access the last element
li[-1] # => 3

# Throw an exception for out-of-bounds.
li[4] # throws an out-of-bounds exception

# Slicing syntax requires indexed access to lists.
# Think of it as left-closed-right-open intervals in math
li[1:3] # => [2, 4]
# Omit the first element
li[2:] # => [4, 3]
# Omit the last element
li[:3] # => [1, 2, 4]

# Delete specific elements
del li[2] # li is now [1, 2, 3]

# Merge the lists
li + other_li # => [1, 2, 3, 4, 5, 6] - doesn't leave the two lists unchanged

# Merge the lists by splicing
li.extend(other_li) # li is [1, 2, 3, 4, 5, 6]

# Use in to return whether an element is in the list or not
1 in li # => True

# Returns the length of the list
len(li) # => 6
```



### III Dictionary-related operations

```python
# Dictionaries are used to store mapping relationships
empty_dict = {}
# Dictionary initialization
filled_dict = {"one": 1, "two": 2, "three": 3}

# Dictionaries are also accessed with middle brackets
filled_dict["one"] # => 1

# Save all the keys in the list
filled_dict.keys() # => ["three", "two", "one"]
# The order of the keys is not unique, what you get is not necessarily in that order

# Save all the values in the list
filled_dict.values() # => [3, 2, 1]
# Same order as the keys

# Determine if a key exists
"one" in filled_dict # => True
1 in filled_dict # => False

# Querying for a non-existent key throws a KeyError
filled_dict["four"] # KeyError

# Use the get method to avoid KeyError
filled_dict.get("one") # => 1
filled_dict.get("four") # => None
# The get method supports returning a default value if none exists.
filled_dict.get("one", 4) # => 1
filled_dict.get("four", 4) # => 4

# setdefault is a safer way to add a dictionary element
filled_dict.setdefault("five", 5) # filled_dict["five"] has a value of 5
filled_dict.setdefault("five", 6) # filled_dict["five"] still has value 5
```



### IV Tuple Related Operations

```python
# A tuple is similar to a list, but it is immutable
tup = (1, 2, 3)
tup[0] # => 1
tup[0] = 3 # type error

# For most list operations, this also applies to tuples
len(tup) # => 3
tup + (4, 5, 6) # => (1, 2, 3, 4, 5, 6)
tup[:2] # => (1, 2)
2 in tup # => True

# You can unwrap a tuple and assign it to multiple variables
a, b, c = (1, 2, 3) # a is 1, b is 2, c is 3
# Without parentheses, they are automatically treated as tuples
d, e, f = 4, 5, 6
# Now we can see how easy it is to exchange two numbers
e, d = d, e # d is 5, e is 4

```

### V. Collections

```python
# Sets store unordered elements
empty_set = set()
# Initialize a set
some_set = set([1, 2, 2, 3, 4]) # some_set is now set([1, 2, 3, 4])

# After Python 2.7, curly braces can be used to represent sets
filled_set = {1, 2, 2, 3, 4} # => {1 2 3 4}

# Add elements to the set
filled_set.add(5) # filled_set is now {1, 2, 3, 4, 5}

# Use & to compute the intersection of sets
other_set = {3, 4, 5, 6}
filled_set & other_set # => {3, 4, 5}

# Use | to compute the union of sets
filled_set | other_set # => {1, 2, 3, 4, 5, 6}

# Use - to compute the difference of sets
{1, 2, 3, 4} - {2, 3, 5} # => {1, 4}

# Use in to determine if an element exists in the set
2 in filled_set # => True
10 in filled_set # => False
```



### VI Lists, dictionaries, tuples, collections, and similarities and differences between them.

1. Similarities: Both are built-in Python data structures that can encapsulate and manipulate multiple data.
2. Differences.
3. - list: an ordered variable sequence, elements can be added, deleted and modified.
   - Dictionary (dict): unordered key-value pairs, the key can not be repeated, you can add and delete key-value pairs, the value can be modified.
   - Set (set): an unordered set of non-repeating elements, you can add and delete elements, but the elements can not be modified.
   - Lists and tuples are sequences, dictionaries and sets are maps.
   - Lists and tuples store elements sequentially, dictionaries and sets are not sequential.
   - Lists and dictionaries have mutable elements, tuples and collections have immutable elements.
   - Dictionaries are stored as key-value pairs, while the other three store only values.
   - Sets do not allow duplicate elements, while the other three do.

In summary, they are different in terms of mutability, ordering, and whether they contain key-value, etc., so you need to choose the right type according to the specific usage scenario.

## Process control

### 一 if...elif...else

```python
# Create a new variable
some_var = 5

# This is an if statement, indentation is important in python.
# The following code snippet will output "some var is smaller than 10"
if some_var > 10.
    print "some_var is totally bigger than 10."
elif some_var < 10: # This elif statement is not required.
    print "some_var is smaller than 10." else: # This else is also not required.
else: # This else is also not required
    print "some_var is indeed 10."
```

### 二 for

```python
"""
Iterate through the list with a for loop
Output.
    dog is a mammal
    cat is a mammal
    mouse is a mammal
"""
for animal in ["dog", "cat", "mouse"].
    # You can format strings with %
    print "%s is a mammal" % animal
    
"""
`range(number)` Returns a list of numbers from 0 to the given number.
Output.
    0
    1
    2
    3
for i in range(4):
    print i
```



### 三 while

```python
"""
while loop
Output: 0
    0
    1
    2
    3
"""
x = 0
while x < 4.
    print x
    x += 1 # shorthand for x = x + 1
```

### 四 try... except

```python
# Handling exceptions with try/except blocks
try.
    # Throw an exception with raise
    raise IndexError("This is an index error")
except IndexError as e: # pass
    pass # pass means do nothing, but usually some recovery work is done here
```



## Functions

### Regular functions ###

```python
# Use def to create a new function
def add(x, y).
    print "x is %s and y is %s" % (x, y)
    return x + y # Return values by return

# Call a function with parameters
add(5, 6) # => output "x is 5 and y is 6" return 11

# Call a function by keyword assignment
add(y=6, x=5) # The order doesn't matter.

# We can also define functions that accept multiple variables that are in order
def varargs(*args).
    return args

varargs(1, 2, 3) # => (1,2,3)


# We can also define functions that accept multiple variables, ordered by keyword
def keyword_args(**kwargs):
    return kwargs

# The actual effect:
keyword_args(big="foot", loch="ness") # => {"big": "foot", "loch": "ness"}

# You can also define a function in both forms at the same time
def all_the_args(*args, **kwargs):
    print args
    print kwargs
"""
all_the_args(1, 2, a=3, b=4) prints.
    (1, 2)
    {"a": 3, "b": 4}
"""

# When calling a function, we can also do the opposite, expanding tuples and dictionaries into arguments
args = (1, 2, 3, 4)
kwargs = {"a": 3, "b": 4}
all_the_args(*args) # Equivalent to foo(1, 2, 3, 4)
all_the_args(**kwargs) # equivalent to foo(a=3, b=4)
all_the_args(*args, **kwargs) # Equivalent to foo(1, 2, 3, 4, a=3, b=4)

# Functions are first class citizens in python
def create_adder(x).
    def adder(y).
        return x + y
    return adder

add_10 = create_adder(10)
add_10(3) # => 13

```



### anonymous function

```python
# anonymous function
(lambda x: x > 2)(3)  # => True
```



### Built-in higher-order functions

```python
# Built-in higher-order functions
map(add_10, [1, 2, 3])  # => [11, 12, 13]
filter(lambda x: x > 5, [3, 4, 5, 6, 7])  # => [6, 7]
```



### advanced technique

```python
# You can use list methods to make more subtle references to higher-order functions
[add_10(i) for i in [1, 2, 3]]  # => [11, 12, 13]
[x for x in [3, 4, 5, 6, 7] if x > 5]  # => [6, 7]
```



## Classes

### Class inheritance

```python
# Our new class inherits from the object class.
class Human(object).

     # Class attribute, shared by all objects of the class
    species = "H. sapiens"

    # Basic constructor
    def __init__(self, name).
        # Assign parameters to object member attributes
        self.name = name

    # Member method with self as argument
    def say(self, msg): # Return "%s: %s" % (self.name).
        return "%s: %s" % (self.name, msg)

    # Class methods are shared by all objects of the class
    # This class method passes the class itself as the first parameter when it is called
    @classmethod
    def get_species(cls):: return cls.species(cls).
        return cls.species

    # Static methods are methods that don't require a reference to a class or an object to be called.
    @staticmethod
    def grunt(): return "*grunt*
        return "*grunt*"
```



### Use of Classes

```python

# Instantiate a class
i = Human(name="Ian")
print i.say("hi") # output "Ian: hi"

j = Human("Joel")
print j.say("hello") # output "Joel: hello"

# Access the methods of the class
i.get_species() # => "H. sapiens"

# Change the shared properties
Human.species = "H. neanderthalensis"
i.get_species() # => "H. neanderthalensis"
j.get_species() # => "H. neanderthalensis"

# Access static variables
human.grunt() # => "*grunt*"
```



## module 

```python
# We can import other modules
import math
print math.sqrt(25) # => 5

# We can also import specific functions from a module
from math import ceil, floor
print ceil(4.7) # => 5.0
print floor(4.7) # => 4.0

# Import all functions from a module
# Warning: not recommended
from math import *

# Shorten the module name
import math as m
math.sqrt(16) == m.sqrt(16) # => True

# Python modules are really just normal python files.
# You can also create your own modules and import them.
# The module name is the same as the file name.

# You can also see what properties and methods are in a module by doing the following
import math
dir(math)
```





