# Python Guide for TypeScript Developers

## Table of Contents
- [Object Model and Memory Management](#object-model-and-memory-management)
- [Core Data Structures](#core-data-structures)
- [Type System](#type-system)
- [Functions and Decorators](#functions-and-decorators)
- [Advanced Features](#advanced-features)
- [Common TypeScript to Python Gotchas](#common-typescript-to-python-gotchas)
- [Performance Considerations](#performance-considerations)
- [Practice Exercises](#practice-exercises)

## Object Model and Memory Management

### Python's "Everything is an Object" Philosophy

In Python, unlike TypeScript, everything is an object. This includes numbers, strings, functions, and classes. Every value has attributes and methods that you can access and modify.

```python
# In TypeScript, primitives and objects are different:
# TypeScript:
# let num = 42;  // primitive
# let obj = new Number(42);  // object wrapper

# In Python, everything is already an object:
number = 42
print(type(number))  # <class 'int'>
print(dir(number))   # Shows all attributes and methods

# Even functions are objects and can have attributes
def my_function():
    pass

my_function.custom_attribute = "Hello"  # Valid in Python!
print(my_function.custom_attribute)     # Prints: Hello
```

Immutable Objects:
int (integers)
float (floating-point numbers)
complex (complex numbers)
bool (booleans)
str (strings)
tuple
frozenset
bytes
Mutable Objects:
list
dict (dictionaries)
set
bytearray
Custom classes (by default)

### Memory Management

Python uses two main mechanisms for memory management:

1. **Reference Counting**: The primary mechanism that tracks how many references point to an object.
```python
import sys

# Create a list
my_list = [1, 2, 3]
# Reference count is 1 (my_list variable)

# Create another reference
another_ref = my_list
# Reference count is now 2

# When references go out of scope or are deleted,
# the count decreases. When it hits 0, the memory is freed
del another_ref  # Count goes back to 1
```

2. **Garbage Collection**: Handles circular references that reference counting can't handle.
```python
class Node:
    def __init__(self):
        self.next = None

# Create a circular reference
node1 = Node()
node2 = Node()
node1.next = node2
node2.next = node1

# Even if we delete our references, reference counting alone
# wouldn't free these objects
del node1
del node2
# Python's garbage collector will eventually clean this up
```

## Core Data Structures

### Lists vs Arrays

Python lists are similar to TypeScript arrays but with important differences:

```python
# Lists can hold different types (like TypeScript arrays)
mixed_list = [1, "hello", {"key": "value"}, [1, 2, 3]]

# Lists are dynamic and resizable
numbers = []
numbers.append(1)  # Automatically resizes as needed

# Slicing - a powerful feature not in TypeScript
full_list = [0, 1, 2, 3, 4, 5]
print(full_list[1:4])    # [1, 2, 3]
print(full_list[::2])    # [0, 2, 4] (step by 2)
print(full_list[::-1])   # [5, 4, 3, 2, 1, 0] (reverse)
```

### Dictionaries

Python dictionaries are hash tables, similar to TypeScript's objects or Map:

```python
# Creating dictionaries
dict1 = {}  # Empty dictionary
dict2 = {"name": "John", "age": 30}

# Key differences from TypeScript objects:
# 1. Keys can be any immutable type
valid_dict = {
    "string_key": 1,
    42: "number as key",
    (1, 2): "tuple as key",
    True: "boolean as key"
}

# 2. Dictionary views (live views of dict data)
my_dict = {"a": 1, "b": 2}
keys = my_dict.keys()    # Dynamic view
my_dict["c"] = 3
print(keys)  # Shows new key too: dict_keys(['a', 'b', 'c'])
```

## Type System

### Dynamic Typing vs TypeScript's Static Typing

Python is dynamically typed, but supports optional type hints:

```python
# Traditional Python (dynamic typing)
def add(a, b):
    return a + b

# Modern Python with type hints (similar to TypeScript)
from typing import List, Dict, Optional

def add_typed(a: int, b: int) -> int:
    return a + b

# Complex types
def process_data(
    items: List[str],
    config: Dict[str, int],
    default: Optional[str] = None
) -> List[str]:
    return items

# Unlike TypeScript, these are just hints and aren't enforced at runtime
def typed_function(x: int) -> str:
    return 42  # This will work! (though it shouldn't)
```

## Functions and Decorators

### Functions as First-Class Objects

Functions in Python are objects that can be passed around:

```python
def greet(name: str) -> str:
    return f"Hello, {name}!"

# Functions can be assigned to variables
greeting = greet
print(greeting("Alice"))  # Works the same as greet("Alice")

# Functions can be passed as arguments
def apply_twice(func, value):
    return func(func(value))

def double(x):
    return x * 2

print(apply_twice(double, 2))  # Prints: 8
```

### Decorators

Decorators are Python's way of modifying or enhancing functions or classes:

```python
# Simple decorator
def log_calls(func):
    def wrapper(*args, **kwargs):
        print(f"Calling {func.__name__}")
        result = func(*args, **kwargs)
        print(f"Finished {func.__name__}")
        return result
    return wrapper

@log_calls
def add(a, b):
    return a + b

# This is equivalent to:
# add = log_calls(add)
```

## Advanced Features

### Context Managers

Context managers handle resource management and cleanup:

```python
# Using with statement
with open("file.txt", "w") as f:
    f.write("Hello")  # File is automatically closed after

# Creating your own context manager
class MyContext:
    def __enter__(self):
        print("Starting")
        return self
        
    def __exit__(self, exc_type, exc_value, traceback):
        print("Cleaning up")
        # Return True to suppress exceptions

# Using the context manager decorator
from contextlib import contextmanager

@contextmanager
def temporary_file(filename):
    try:
        f = open(filename, "w")
        yield f
    finally:
        f.close()
        import os
        os.remove(filename)
```

### Generators

Generators provide memory-efficient iteration:

```python
# Simple generator function
def count_up_to(n):
    i = 0
    while i < n:
        yield i
        i += 1

# Using the generator
for num in count_up_to(5):
    print(num)  # Prints 0, 1, 2, 3, 4

# Generator expressions
squares = (x*x for x in range(1000000))  # Memory efficient
# vs list comprehension
squares_list = [x*x for x in range(1000000)]  # Creates full list in memory
```

## Common TypeScript to Python Gotchas

### Mutable Default Arguments

```python
# This is a common mistake:
def append_to(item, lst=[]):  # DON'T DO THIS!
    lst.append(item)
    return lst

print(append_to(1))  # [1]
print(append_to(2))  # [1, 2]  # Surprise!

# Correct way:
def append_to_fixed(item, lst=None):
    if lst is None:
        lst = []
    lst.append(item)
    return lst
```

### Variable Scoping

```python
# Python's scoping rules are different from TypeScript
x = 0

def outer():
    x = 1  # Creates new local variable
    def inner():
        print(x)  # Would error if we tried to modify x
    inner()

# Using nonlocal and global
def outer_fixed():
    x = 1
    def inner():
        nonlocal x  # Now we can modify outer x
        x = 2
    inner()
```

## Performance Considerations

### The Global Interpreter Lock (GIL)

The GIL is a mutex that makes Python thread-unsafe for CPU-bound tasks:

```python
import threading
import time

# CPU-bound tasks don't benefit from threading
def cpu_bound():
    for i in range(10**7):
        _ = i ** 2

# Run sequentially
start = time.time()
cpu_bound()
cpu_bound()
print(f"Sequential: {time.time() - start}")

# Run with threads (not faster due to GIL)
start = time.time()
t1 = threading.Thread(target=cpu_bound)
t2 = threading.Thread(target=cpu_bound)
t1.start(); t2.start()
t1.join(); t2.join()
print(f"Threaded: {time.time() - start}")
```

### Performance Tips

```python
# String concatenation
# Bad:
s = ""
for i in range(1000):
    s += str(i)  # Creates many temporary strings

# Good:
parts = []
for i in range(1000):
    parts.append(str(i))
result = "".join(parts)

# List operations
from collections import deque
# Bad for large lists:
my_list = [1, 2, 3, 4, 5]
my_list.pop(0)  # O(n) operation

# Good:
my_queue = deque([1, 2, 3, 4, 5])
my_queue.popleft()  # O(1) operation
```

## Practice Exercises

1. Implement a class that behaves like a list but maintains a running average of all elements
2. Create a decorator that caches function results based on arguments
3. Write a context manager for temporary file creation and cleanup
4. Implement a custom dictionary that allows dot notation for accessing keys
5. Create a generator that yields prime numbers efficiently

## Additional Resources

- [Python Language Reference](https://docs.python.org/3/reference/)
- [Python Data Model](https://docs.python.org/3/reference/datamodel.html)
- [Type Hints PEP 484](https://www.python.org/dev/peps/pep-0484/)
- [Python Design Patterns](https://python-patterns.guide/)

## Contributing

Feel free to submit issues and enhancement requests!

## License

This guide is released under the MIT License. See the LICENSE file for details.
