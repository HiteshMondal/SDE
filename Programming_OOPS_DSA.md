# Python Complete Guide

A single-file reference covering core Python concepts, built-ins, coding patterns, tricks, DSA, and OOP — each with a working code example.

---

# Basics

## Variables and Data Types

```python
name = "Alice"          # str
age = 25                 # int
height = 5.6              # float
is_active = True          # bool
data = None                # NoneType

print(type(age))  # <class 'int'>
```

## Type Casting (Quick Intro)

```python
x = int("10")       # 10
y = float("3.14")   # 3.14
z = str(100)         # "100"
b = bool(0)           # False
```

---

# Data Type Conversion (All Types)

## int → other types

```python
int_val = 10

str(int_val)      # "10"
float(int_val)      # 10.0
bool(int_val)          # True  (0 -> False, any nonzero -> True)
complex(int_val)          # (10+0j)

arr = []
while n > 0:
  temp = n % 10    # %10 shows the last digit
  n //= 10         # //10 removes last digit
  arr.insert(0, temp)
```

## float → other types

```python
float_val = 3.99

int(float_val)      # 3   (truncates, does not round)
str(float_val)         # "3.99"
bool(float_val)           # True (0.0 -> False)
```

## str → other types

```python
int("25")               # 25   (must contain only digits/sign)
float("3.14")              # 3.14
bool("")                     # False (empty string is falsy)
bool("False")                  # True  (non-empty string is truthy, even "False"!)
list("abc")                       # ['a', 'b', 'c']
tuple("abc")                        # ('a', 'b', 'c')
set("aab")                             # {'a', 'b'}
int("101", 2)                            # 5   (binary string -> int, base=2)
int("ff", 16)                              # 255 (hex string -> int, base=16)
```

## bool → other types

```python
bool_val = True

int(bool_val)      # 1
float(bool_val)       # 1.0
str(bool_val)            # "True"
```

## list ↔ tuple ↔ set

```python
lst = [1, 2, 2, 3]

tuple(lst)          # (1, 2, 2, 3)
set(lst)               # {1, 2, 3}  (removes duplicates)

tpl = (1, 2, 3)
list(tpl)              # [1, 2, 3]
set(tpl)                  # {1, 2, 3}

st = {1, 2, 3}
list(st)                    # [1, 2, 3]  (order not guaranteed)
tuple(st)                      # (1, 2, 3)

s = "dog cat"
words = s.split()       # ['dog', 'cat'] (Use space as separator)
```

## list/tuple ↔ str

```python
lst = ['P', 'y', 't', 'h', 'o', 'n']
"".join(lst)          # "Python"   (list of chars -> string)

s = "Python"
list(s)                  # ['P','y','t','h','o','n']

words = ["I", "love", "Python"]
" ".join(words)             # "I love Python" (Syntax: "separator".join(iterable))
"I love Python".split()     # ["I", "love", "Python"]

result = ""
for num in list:
  result += str(num)
result int(result)

```

## dict ↔ list of tuples

```python
d = {"a": 1, "b": 2}

list(d.items())      # [('a', 1), ('b', 2)]
list(d.keys())          # ['a', 'b']
list(d.values())           # [1, 2]

pairs = [("a", 1), ("b", 2)]
dict(pairs)               # {'a': 1, 'b': 2}

keys = ["a", "b", "c"]
dict.fromkeys(keys, 0)       # {'a': 0, 'b': 0, 'c': 0}
```

## list ↔ set ↔ dict (frequency map)

```python
lst = ["a", "b", "a", "c"]
set(lst)                        # {'a', 'b', 'c'}   unique values

from collections import Counter
dict(Counter(lst))                 # {'a': 2, 'b': 1, 'c': 1}
```

## Number base conversions

```python
bin(10)          # '0b1010'
oct(10)             # '0o12'
hex(10)                # '0xa'

int('1010', 2)            # 10   binary string -> int
int('12', 8)                  # 10   octal string -> int
int('a', 16)                     # 10   hex string -> int
```

## char ↔ ASCII/unicode code

```python
ord('A')          # 65   char -> code point
chr(65)               # 'A'  code point -> char
```

## JSON ↔ Python objects

```python
import json

data = {"name": "Alice", "age": 25}
json_str = json.dumps(data)      # dict -> JSON string
back = json.loads(json_str)         # JSON string -> dict
```

## Common Pitfalls

```python
int("3.14")        # ValueError: can't parse a float-looking string directly
int(float("3.14"))    # 3   -> convert to float first, then int

bool("0")             # True   (non-empty string, even "0", is truthy!)
bool(0)                  # False  (only the number 0 is falsy)

list("123")                # ['1', '2', '3']  (splits into chars, not [123])
```

## Operators

```python
# Arithmetic
print(7 // 2, 7 % 2, 2 ** 3)   # 3 1 8

# Comparison / Logical
print(5 > 3 and 2 < 4)          # True

# Bitwise
a, b = 5, 3                     # 101, 011

print(a & b)                    # 1  → AND: 001
print(a | b)                    # 7  → OR:  111
print(a ^ b)                    # 6  → XOR: 110
print(~a)                       # -6 → NOT
print(a << 1)                   # 10 → left shift (× 2)
print(a >> 1)                   # 2  → right shift (÷ 2)

# Walrus operator (assignment inside expression)
if (n := 10) > 5:
    print(n)                    # 10
```

### Bitwise Quick Rules

```python
x = 1 << 3                      # 8 → set the 3rd bit
print(x & 1)                    # 0 → check if even
print(x | 2)                    # 10
print(x ^ x)                    # 0 → XOR with itself
```

* `&` → AND
* `|` → OR
* `^` → XOR
* `~` → NOT
* `<<` → left shift
* `>>` → right shift

**Interview tip:** Bitwise operators are commonly used for **flags, masks, checking/set/clearing bits, XOR problems, and powers of 2**.

---

# Loops

## for Loop

```python
for i in range(5):
    print(i)   # 0 1 2 3 4

for ch in "abc":
    print(ch)   # a b c

for item in [10, 20, 30]:
    print(item)
```

## while Loop

```python
n = 5
while n > 0:
    print(n)
    n -= 1
```

## range() Variations

```python
range(5)          # 0..4
range(2, 8)         # 2..7
range(0, 10, 2)       # 0,2,4,6,8
range(10, 0, -1)        # 10..1 (reverse)
```

## break, continue, pass

```python
for i in range(10):
    if i == 5:
        break        # exit loop entirely
    if i % 2 == 0:
        continue      # skip to next iteration
    print(i)            # 1 3

for i in range(3):
    pass                  # placeholder, does nothing
```

## else Clause on Loops

```python
# else runs only if the loop completes without a 'break'
for i in range(5):
    if i == 10:
        break
else:
    print("Loop finished without break")
```

## Nested Loops

```python
for i in range(3):
    for j in range(3):
        print(i, j)

# break only exits the innermost loop
for i in range(3):
    for j in range(3):
        if j == 1:
            break
        print(i, j)
```

## Looping with enumerate() and zip()

```python
fruits = ["apple", "banana", "cherry"]
for index, fruit in enumerate(fruits):
    print(index, fruit)          # 0 apple, 1 banana, 2 cherry

names = ["Alice", "Bob"]
scores = [90, 85]
for name, score in zip(names, scores):
    print(name, score)             # Alice 90, Bob 85
```

## Looping Over Dictionaries

```python
d = {"a": 1, "b": 2}
for key in d:
    print(key)

for key, value in d.items():
    print(key, value)
```

## Infinite Loop with Controlled Exit

```python
count = 0
while True:
    count += 1
    if count > 3:
        break
    print(count)   # 1 2 3
```

## Loop-Based Comprehension Shortcuts

```python
squares = [x*x for x in range(5)]           # list comprehension loop
total = sum(x for x in range(10))              # generator expression loop
matrix = [[i*j for j in range(3)] for i in range(3)]   # nested loop comprehension
```

---

# Strings

## Common String Methods

```python
s = "  Hello World  "

s.strip()          # "Hello World"
s.lower()           # "  hello world  "
s.upper()            # "  HELLO WORLD  "
s.replace("World", "Python")  # "  Hello Python  "
s.split()             # ['Hello', 'World']
"-".join(["a","b","c"])  # "a-b-c"
s.find("World")        # index of substring
s.count("o")             # count occurrences
s.startswith("  He")      # True
"abc".zfill(5)              # "00abc"
```

## String Formatting

```python
name, age = "Bob", 30
print(f"{name} is {age} years old")     # f-string
print("{} is {}".format(name, age))       # .format()
print("%s is %d" % (name, age))              # % formatting
```

## Slicing

```python
s = "Programming"

print(s[0:6])       # 'Progra'       → start included, end excluded
print(s[::-1])      # 'gnimmargorP'  → reverse the string
print(s[::2])       # 'Pormig'       → every 2nd character
```

### Tricky Slicing Examples

```python
s = "Programming"

print(s[2:9:2])     # 'ormn'         → index 2 to 8, step 2
print(s[-6:-1])     # 'rammin'       → negative indexes
print(s[-1:-6:-1])  # 'gnimm'        → move backwards
print(s[8:2:-2])    # 'rgo'          → backwards with step 2
print(s[:4])        # 'Prog'         → from beginning
print(s[4:])        # 'ramming'      → to the end
print(s[::-2])      # 'gimroP'       → reverse, taking every 2nd char
```

### Important Rule

```python
s[start:stop:step]
```

* `start` → included
* `stop` → excluded
* `step` → direction and jump size
* Negative `step` → move from right to left

```python
s = "abcdefg"

print(s[1:6:2])     # 'bdf'
print(s[6:1:-2])    # 'gec'
print(s[-2::-2])    # 'fdb'
print(s[:3:-1])     # 'gfed'
```

**Info:** It is same for numbers also

---

# Numbers and Math

```python
import math

abs(-5)             # 5
round(3.14159, 2)     # 3.14
pow(2, 10)             # 1024
gcd(24, 36)            # 12 (HCF)
lcm(12, 18)            # 36
perm(5, 2)               # 20 (Permutation)
comb(5, 2)               # 10 (Combination)
math.sqrt(16)            # 4.0 or int(16 ** 0.5)
math.ceil(4.1)             # 5
math.floor(4.9)              # 4
max(3, 7, 2), min(3, 7, 2)     # 7, 2
sum([1, 2, 3])                   # 6
divmod(9, 2)                       # (4, 1)
isalnum()                  # Check Alphanumberic
isalpha()                  # Check Alphabet
isdigit()                  # Digit
isnumeric()                # same as digit() but include fractions
isspace()                  # Check space
```

---

# Collections

## Lists

```python
lst = [3, 1, 4, 1, 5]

lst.append(9)          # add to end
lst.insert(0, 100)       # insert at index
lst.remove(1)              # remove first matching value
lst.pop()                    # remove & return last item, pop(2): remove index 2
lst.sort()                     # sort in place
lst.sort(reverse=True)          # descending
sorted(lst)                       # returns new sorted list
lst.reverse()                       # reverse in place
lst.index(4)                          # find index of value
lst.count(1)                            # count occurrences
lst.extend([7, 8])                        # merge another list
```

### List Comprehension

```python
squares = [x*x for x in range(10)]
evens = [x for x in range(20) if x % 2 == 0]
matrix_flat = [x for row in [[1,2],[3,4]] for x in row]
```

## Tuples

```python
t = (1, 2, 3)
a, b, c = t              # unpacking
t2 = t + (4, 5)             # concatenation (immutable)
```

## Dictionaries

```python
d = {"a": 1, "b": 2}

d.get("c", 0)            # default value if key missing
d.keys()                    # dict_keys(['a', 'b'])
d.values()                    # dict_values([1, 2])
d.items()                       # dict_items([('a',1),('b',2)])
d.update({"c": 3})                # add/update key
d.pop("a")                          # remove key, return value
d.setdefault("d", 10)                 # set if not exists

# Dict comprehension
squares = {x: x*x for x in range(5)}
```

## Sets

```python
s1 = {1, 2, 3}
s2 = {2, 3, 4}

s1.union(s2)                # {1,2,3,4}
s1.intersection(s2)           # {2,3}
s1.difference(s2)               # {1}
s1.symmetric_difference(s2)       # {1,4}
s1.add(10)
s1.discard(1)
```

---

# Built-in Functions (Essential)

```python
len([1,2,3])                      # 3
range(1, 10, 2)                     # 1,3,5,7,9
enumerate(["a","b"])                  # (0,'a'), (1,'b')
zip([1,2], ["a","b"])                   # (1,'a'), (2,'b')
map(lambda x: x*2, [1,2,3])               # [2,4,6]
filter(lambda x: x % 2 == 0, [1,2,3,4])     # [2,4]
sorted([3,1,2], key=lambda x: -x)             # [3,2,1]
any([False, True]), all([True, True])           # True, True
isinstance(5, int)                                 # True
id(5), hex(255), bin(5), oct(8)                      # memory id, hex, binary, octal
```

### `map`, `filter`, `zip` in action

```python
nums = [1, 2, 3, 4, 5]
doubled = list(map(lambda x: x * 2, nums))
evens = list(filter(lambda x: x % 2 == 0, nums))
paired = list(zip(nums, doubled))
print(doubled, evens, paired)
```

---

# Functions

## Basics, *args, **kwargs

```python
def greet(name, greeting="Hello"):
    return f"{greeting}, {name}!"

def total(*args, **kwargs):
    print(args)       # tuple of positional args
    print(kwargs)        # dict of keyword args
    return sum(args)

total(1, 2, 3, tax=0.1)
```

## Lambda Functions

```python
add = lambda x, y: x + y
print(add(3, 4))   # 7
```

## Decorators

```python
def logger(func):
    def wrapper(*args, **kwargs):
        print(f"Calling {func.__name__}")
        return func(*args, **kwargs)
    return wrapper

@logger
def say_hi():
    print("Hi!")

say_hi()
```

## Generators (yield)

```python
def countdown(n):
    while n > 0:
        yield n
        n -= 1

for num in countdown(3):
    print(num)   # 3 2 1
```

## Closures

```python
def outer(x):
    def inner(y):
        return x + y
    return inner

add5 = outer(5)
print(add5(10))   # 15
```

---

# Modules & Packages

## Creating and Importing a Module

```python
# file: mymath.py
def add(a, b):
    return a + b

def subtract(a, b):
    return a - b
```

```python
# file: main.py
import mymath

print(mymath.add(2, 3))          # 5

from mymath import subtract
print(subtract(5, 2))               # 3

import mymath as mm
print(mm.add(1, 1))                    # 2

from mymath import *                     # imports everything (avoid in large projects)
```

## Package Structure

```text
myproject/
│
├── mypackage/
│   ├── __init__.py       # marks folder as a package
│   ├── module_a.py
│   └── module_b.py
│
└── main.py
```

```python
# mypackage/__init__.py
from .module_a import func_a
from .module_b import func_b
```

```python
# main.py
from mypackage import func_a, func_b
```

## Relative vs Absolute Imports

```python
# absolute import
from mypackage.module_a import func_a

# relative import (used inside a package)
from . import module_a
from .module_b import func_b
```

## `__name__ == "__main__"` Pattern

```python
# file: script.py
def main():
    print("Running script directly")

if __name__ == "__main__":
    main()          # runs only when executed directly, not when imported
```

## Standard Library Useful Modules

```python
import os
os.getcwd()                    # current working directory
os.listdir(".")                   # list files in directory

import sys
sys.argv                       # command-line arguments
sys.path                          # module search paths

import datetime
datetime.datetime.now()

import random
random.randint(1, 10)

import re
re.findall(r"\d+", "abc123def456")   # ['123', '456']
```

## Installing & Using Third-Party Packages

```bash
pip install requests
```

```python
import requests
```

## Virtual Environments (isolate project dependencies)

```bash
python -m venv venv
source venv/bin/activate      # Linux/macOS
venv\Scripts\activate         # Windows

pip install -r requirements.txt
pip freeze > requirements.txt
```

---

# API Calls

## GET Request

```python
import requests

response = requests.get("https://api.github.com/users/octocat")

print(response.status_code)   # 200
print(response.json())           # parsed JSON response as a dict
```

## GET with Query Parameters

```python
import requests

params = {"q": "python", "sort": "stars"}
response = requests.get("https://api.github.com/search/repositories", params=params)
data = response.json()
```

## POST Request

```python
import requests

payload = {"title": "Hello", "body": "World", "userId": 1}
response = requests.post("https://jsonplaceholder.typicode.com/posts", json=payload)

print(response.status_code)   # 201
print(response.json())
```

## Headers and Authentication

```python
import requests

headers = {
    "Authorization": "Bearer YOUR_TOKEN",
    "Content-Type": "application/json"
}

response = requests.get("https://api.example.com/data", headers=headers)
```

## PUT and DELETE

```python
import requests

# Update
requests.put("https://jsonplaceholder.typicode.com/posts/1", json={"title": "Updated"})

# Delete
requests.delete("https://jsonplaceholder.typicode.com/posts/1")
```

## Handling Errors and Timeouts

```python
import requests

try:
    response = requests.get("https://api.example.com/data", timeout=5)
    response.raise_for_status()      # raises an error for 4xx/5xx status codes
    data = response.json()
except requests.exceptions.Timeout:
    print("Request timed out")
except requests.exceptions.HTTPError as e:
    print(f"HTTP error: {e}")
except requests.exceptions.RequestException as e:
    print(f"Request failed: {e}")
```

## Async API Calls (with aiohttp)

```python
import aiohttp
import asyncio

async def fetch(session, url):
    async with session.get(url) as response:
        return await response.json()

async def main():
    async with aiohttp.ClientSession() as session:
        data = await fetch(session, "https://api.github.com/users/octocat")
        print(data)

asyncio.run(main())
```

## Building a Simple API (with Flask)

```python
from flask import Flask, jsonify, request

app = Flask(__name__)

@app.route("/hello", methods=["GET"])
def hello():
    return jsonify({"message": "Hello, World!"})

@app.route("/add", methods=["POST"])
def add():
    data = request.get_json()
    result = data["a"] + data["b"]
    return jsonify({"result": result})

if __name__ == "__main__":
    app.run(debug=True)
```

---

# Exception Handling

```python
try:
    x = 10 / 0
except ZeroDivisionError as e:
    print(f"Error: {e}")
except Exception as e:
    print(f"Other error: {e}")
else:
    print("No error occurred")
finally:
    print("Always runs")

# Custom exception
class InvalidAgeError(Exception):
    pass

def check_age(age):
    if age < 0:
        raise InvalidAgeError("Age cannot be negative")
```

---

# File Handling

```python
# Writing
with open("data.txt", "w") as f:
    f.write("Hello, file!")

# Reading
with open("data.txt", "r") as f:
    content = f.read()
    lines = f.readlines()
```

---

# Asynchronous Programming

Async code lets a program handle multiple I/O-bound tasks (network calls, file reads, DB queries) without blocking, using a single-threaded event loop instead of multiple threads.

## async / await Basics

```python
import asyncio

async def say_hello():
    print("Start")
    await asyncio.sleep(1)   # non-blocking wait
    print("End")

asyncio.run(say_hello())
```

## Running Tasks Concurrently

```python
import asyncio

async def fetch_data(name, delay):
    await asyncio.sleep(delay)
    print(f"{name} done")
    return name

async def main():
    results = await asyncio.gather(
        fetch_data("Task1", 2),
        fetch_data("Task2", 1),
        fetch_data("Task3", 3)
    )
    print(results)   # runs concurrently, finishes in ~3s not 6s

asyncio.run(main())
```

## Creating Tasks (fire-and-forget style)

```python
import asyncio

async def worker(n):
    await asyncio.sleep(1)
    return n * n

async def main():
    task1 = asyncio.create_task(worker(2))
    task2 = asyncio.create_task(worker(3))
    print(await task1, await task2)   # 4 9

asyncio.run(main())
```

## async for and async with

```python
import asyncio

async def async_generator():
    for i in range(3):
        await asyncio.sleep(0.5)
        yield i

async def main():
    async for value in async_generator():
        print(value)   # 0 1 2

asyncio.run(main())
```

## Timeouts

```python
import asyncio

async def slow_task():
    await asyncio.sleep(5)

async def main():
    try:
        await asyncio.wait_for(slow_task(), timeout=2)
    except asyncio.TimeoutError:
        print("Task timed out")

asyncio.run(main())
```

**Key rule:** `async def` marks a coroutine, `await` pauses until it resolves, and `asyncio.run()` starts the event loop. Never use `time.sleep()` inside async code — use `asyncio.sleep()`.

---
---

# Object-Oriented Programming (OOP)

## Classes and Objects

```python
class Car:
    wheels = 4   # class attribute

    def __init__(self, brand, model):
        self.brand = brand      # instance attribute
        self.model = model

    def info(self):
        return f"{self.brand} {self.model}"

car1 = Car("Toyota", "Corolla")
print(car1.info())
```

## Inheritance

```python
class Vehicle:
    def __init__(self, brand):
        self.brand = brand

    def describe(self):
        return f"Vehicle: {self.brand}"

class Car(Vehicle):
    def __init__(self, brand, model):
        super().__init__(brand)
        self.model = model

    def describe(self):
        return f"{super().describe()} Model: {self.model}"

print(Car("Honda", "Civic").describe())
```

## Polymorphism

```python
class Cat:
    def speak(self):
        return "Meow"

class Dog:
    def speak(self):
        return "Woof"

for animal in [Cat(), Dog()]:
    print(animal.speak())
```

## Encapsulation

```python
class BankAccount:
    def __init__(self, balance):
        self.__balance = balance   # private attribute

    def deposit(self, amount):
        self.__balance += amount

    def get_balance(self):
        return self.__balance

acc = BankAccount(100)
acc.deposit(50)
print(acc.get_balance())   # 150
```

## Abstraction

```python
from abc import ABC, abstractmethod

class Shape(ABC):
    @abstractmethod
    def area(self):
        pass

class Circle(Shape):
    def __init__(self, radius):
        self.radius = radius

    def area(self):
        return 3.14 * self.radius ** 2

print(Circle(5).area())
```

## Magic / Dunder Methods

```python
class Point:
    def __init__(self, x, y):
        self.x, self.y = x, y

    def __str__(self):
        return f"({self.x}, {self.y})"

    def __add__(self, other):
        return Point(self.x + other.x, self.y + other.y)

    def __eq__(self, other):
        return self.x == other.x and self.y == other.y

p1, p2 = Point(1, 2), Point(3, 4)
print(p1 + p2)        # (4, 6)
print(p1 == p2)          # False
```

## Class Methods and Static Methods

```python
class Employee:
    count = 0

    def __init__(self, name):
        self.name = name
        Employee.count += 1

    @classmethod
    def total_employees(cls):
        return cls.count

    @staticmethod
    def is_valid_name(name):
        return len(name) > 0

print(Employee.total_employees())
```

---

# Iterators and Iterables

```python
class Counter:
    def __init__(self, limit):
        self.limit = limit
        self.n = 0

    def __iter__(self):
        return self

    def __next__(self):
        if self.n < self.limit:
            self.n += 1
            return self.n
        raise StopIteration

for num in Counter(3):
    print(num)   # 1 2 3
```

---

# Generics (Type Hints & Generic Programming)

Generics let functions and classes work with multiple types while still being type-checked (useful with `mypy` and IDEs).

## Basic Type Hints

```python
def add(a: int, b: int) -> int:
    return a + b

def greet(name: str) -> str:
    return f"Hello, {name}"
```

## Generic Functions with TypeVar

```python
from typing import TypeVar, List

T = TypeVar("T")

def first_element(items: List[T]) -> T:
    return items[0]

print(first_element([1, 2, 3]))       # 1
print(first_element(["a", "b"]))        # "a"
```

## Generic Classes

```python
from typing import Generic, TypeVar

T = TypeVar("T")

class Box(Generic[T]):
    def __init__(self, item: T):
        self.item = item

    def get(self) -> T:
        return self.item

int_box = Box(123)
str_box = Box("hello")
print(int_box.get(), str_box.get())
```

## Bounded TypeVar (restrict allowed types)

```python
from typing import TypeVar

Number = TypeVar("Number", int, float)

def double(x: Number) -> Number:
    return x * 2

print(double(5))       # 10
print(double(2.5))       # 5.0
```

## Modern Generic Syntax (Python 3.12+)

```python
def first(items: list[T]) -> T:
    return items[0]

class Stack[T]:
    def __init__(self):
        self.items: list[T] = []

    def push(self, item: T) -> None:
        self.items.append(item)

    def pop(self) -> T:
        return self.items.pop()
```

## Optional and Union Types

```python
from typing import Optional, Union

def find_user(user_id: int) -> Optional[str]:
    return "Alice" if user_id == 1 else None

def process(value: Union[int, str]) -> str:
    return str(value)
```

**Key rule:** Generics document *what type goes in and out* without hardcoding a single type — the same function/class works safely across `int`, `str`, custom objects, etc.

---

# Python Tricks and Shortcuts

```python
# Swap variables
a, b = 1, 2
a, b = b, a

# Multiple assignment
x = y = z = 0

# Unpacking with *
first, *middle, last = [1, 2, 3, 4, 5]

# Ternary operator
status = "adult" if age >= 18 else "minor"

# Merge dictionaries (Python 3.9+)
d1, d2 = {"a": 1}, {"b": 2}
merged = d1 | d2

# Flatten a list of lists
nested = [[1, 2], [3, 4]]
flat = [x for sub in nested for x in sub]

# Counting elements
from collections import Counter
Counter("mississippi")   # {'i': 4, 's': 4, 'p': 2, 'm': 1}

# Default dictionary
from collections import defaultdict
dd = defaultdict(int)
dd["a"] += 1

# Reverse a list quickly
lst = [1, 2, 3]
lst[::-1]

# Chained comparison
print(1 < 5 < 10)   # True

# One-liner FizzBuzz
print(["Fizz"*(i%3==0)+"Buzz"*(i%5==0) or str(i) for i in range(1,16)])
```

---

# Coding Patterns (Problem-Solving Templates)

## Two Pointers

```python
def is_palindrome(s):
    left, right = 0, len(s) - 1
    while left < right:
        if s[left] != s[right]:
            return False
        left += 1
        right -= 1
    return True
```

### Opposite Direction

```python
def two_sum_sorted(arr, target):
    left, right = 0, len(arr) - 1

    while left < right:
        total = arr[left] + arr[right]

        if total == target:
            return [left, right]
        elif total < target:
            left += 1
        else:
            right -= 1

    return []
```

### Same Direction / Fast and Slow

```python
def remove_duplicates(nums):
    if not nums:
        return 0

    slow = 0

    for fast in range(1, len(nums)):
        if nums[fast] != nums[slow]:
            slow += 1
            nums[slow] = nums[fast]

    return slow + 1
```

### Partition Around a Condition

```python
def move_zeroes(nums):
    left = 0

    for right in range(len(nums)):
        if nums[right] != 0:
            nums[left], nums[right] = nums[right], nums[left]
            left += 1
```

## Sliding Window

```python
def max_sum_subarray(arr, k):
    window_sum = sum(arr[:k])
    max_sum = window_sum

    for i in range(k, len(arr)):
        window_sum += arr[i] - arr[i - k]
        max_sum = max(max_sum, window_sum)

    return max_sum
```

### Variable-Size Window

```python
def longest_subarray_sum_at_most_k(nums, k):
    left = 0
    window_sum = 0
    result = 0

    for right in range(len(nums)):
        window_sum += nums[right]

        while window_sum > k:
            window_sum -= nums[left]
            left += 1

        result = max(result, right - left + 1)

    return result
```

### Longest Substring Without Repeating Characters

```python
def longest_unique_substring(s):
    seen = set()
    left = 0
    result = 0

    for right in range(len(s)):
        while s[right] in seen:
            seen.remove(s[left])
            left += 1

        seen.add(s[right])
        result = max(result, right - left + 1)

    return result
```

### Frequency-Based Window

```python
from collections import Counter

def min_window(s, t):
    need = Counter(t)
    have = {}
    formed = 0
    required = len(need)

    left = 0
    best = ""

    for right, ch in enumerate(s):
        have[ch] = have.get(ch, 0) + 1

        if ch in need and have[ch] == need[ch]:
            formed += 1

        while formed == required:
            current = s[left:right + 1]

            if not best or len(current) < len(best):
                best = current

            have[s[left]] -= 1

            if s[left] in need and have[s[left]] < need[s[left]]:
                formed -= 1

            left += 1

    return best
```

## Fast and Slow Pointers (Cycle Detection)

```python
def has_cycle(head):
    slow = fast = head

    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next

        if slow == fast:
            return True

    return False
```

### Find Cycle Entry

```python
def cycle_start(head):
    slow = fast = head

    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next

        if slow == fast:
            break
    else:
        return None

    slow = head

    while slow != fast:
        slow = slow.next
        fast = fast.next

    return slow
```

### Find Middle of Linked List

```python
def find_middle(head):
    slow = fast = head

    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next

    return slow
```

## Binary Search

```python
def binary_search(arr, target):
    low, high = 0, len(arr) - 1

    while low <= high:
        mid = (low + high) // 2

        if arr[mid] == target:
            return mid
        elif arr[mid] < target:
            low = mid + 1
        else:
            high = mid - 1

    return -1
```

### First Occurrence

```python
def first_position(arr, target):
    low, high = 0, len(arr) - 1
    answer = -1

    while low <= high:
        mid = (low + high) // 2

        if arr[mid] >= target:
            if arr[mid] == target:
                answer = mid
            high = mid - 1
        else:
            low = mid + 1

    return answer
```

### Last Occurrence

```python
def last_position(arr, target):
    low, high = 0, len(arr) - 1
    answer = -1

    while low <= high:
        mid = (low + high) // 2

        if arr[mid] <= target:
            if arr[mid] == target:
                answer = mid
            low = mid + 1
        else:
            high = mid - 1

    return answer
```

### Binary Search on Answer

```python
def can_finish(tasks, capacity, days):
    required_days = 1
    current = 0

    for task in tasks:
        if current + task > capacity:
            required_days += 1
            current = 0

        current += task

    return required_days <= days


def minimum_capacity(tasks, days):
    low = max(tasks)
    high = sum(tasks)

    while low < high:
        mid = (low + high) // 2

        if can_finish(tasks, mid, days):
            high = mid
        else:
            low = mid + 1

    return low
```

## Backtracking

```python
def subsets(nums):
    result = []

    def backtrack(start, path):
        result.append(path[:])

        for i in range(start, len(nums)):
            path.append(nums[i])
            backtrack(i + 1, path)
            path.pop()

    backtrack(0, [])
    return result
```

### Permutations

```python
def permutations(nums):
    result = []

    def backtrack(path, used):
        if len(path) == len(nums):
            result.append(path[:])
            return

        for i in range(len(nums)):
            if used[i]:
                continue

            used[i] = True
            path.append(nums[i])

            backtrack(path, used)

            path.pop()
            used[i] = False

    backtrack([], [False] * len(nums))
    return result
```

### Combination Sum

```python
def combination_sum(nums, target):
    result = []

    def backtrack(start, path, total):
        if total == target:
            result.append(path[:])
            return

        if total > target:
            return

        for i in range(start, len(nums)):
            path.append(nums[i])
            backtrack(i, path, total + nums[i])
            path.pop()

    backtrack(0, [], 0)
    return result
```

### Grid Backtracking

```python
def word_exists(board, word):
    rows, cols = len(board), len(board[0])

    def dfs(r, c, index):
        if index == len(word):
            return True

        if (
            r < 0 or r >= rows or
            c < 0 or c >= cols or
            board[r][c] != word[index]
        ):
            return False

        original = board[r][c]
        board[r][c] = "#"

        found = (
            dfs(r + 1, c, index + 1) or
            dfs(r - 1, c, index + 1) or
            dfs(r, c + 1, index + 1) or
            dfs(r, c - 1, index + 1)
        )

        board[r][c] = original
        return found

    for r in range(rows):
        for c in range(cols):
            if dfs(r, c, 0):
                return True

    return False
```

## Dynamic Programming (Memoization)

```python
def fib(n, memo={}):
    if n in memo:
        return memo[n]

    if n <= 1:
        return n

    memo[n] = fib(n - 1, memo) + fib(n - 2, memo)
    return memo[n]
```

### Recommended Explicit Memo Pattern

```python
def solve(n, memo):
    if n in memo:
        return memo[n]

    if n == 0:
        return 0

    result = solve(n - 1, memo)

    memo[n] = result
    return result


memo = {}
answer = solve(10, memo)
```

### 2D Memoization

```python
def solve(row, col, memo):
    if row < 0 or col < 0:
        return 0

    if row == 0 and col == 0:
        return 1

    if (row, col) in memo:
        return memo[(row, col)]

    memo[(row, col)] = (
        solve(row - 1, col, memo) +
        solve(row, col - 1, memo)
    )

    return memo[(row, col)]
```

## Dynamic Programming (Tabulation)

```python
def climb_stairs(n):
    dp = [0] * (n + 1)
    dp[0], dp[1] = 1, 1

    for i in range(2, n + 1):
        dp[i] = dp[i - 1] + dp[i - 2]

    return dp[n]
```

### 2D DP

```python
def unique_paths(rows, cols):
    dp = [[0] * cols for _ in range(rows)]

    for r in range(rows):
        dp[r][0] = 1

    for c in range(cols):
        dp[0][c] = 1

    for r in range(1, rows):
        for c in range(1, cols):
            dp[r][c] = dp[r - 1][c] + dp[r][c - 1]

    return dp[rows - 1][cols - 1]
```

### Space-Optimized DP

```python
def unique_paths(rows, cols):
    dp = [1] * cols

    for _ in range(1, rows):
        for c in range(1, cols):
            dp[c] += dp[c - 1]

    return dp[-1]
```

## Dynamic Programming (Knapsack)

```python
def knapsack(weights, values, capacity):
    n = len(weights)
    dp = [[0] * (capacity + 1) for _ in range(n + 1)]

    for i in range(1, n + 1):
        weight = weights[i - 1]
        value = values[i - 1]

        for capacity_left in range(capacity + 1):
            dp[i][capacity_left] = dp[i - 1][capacity_left]

            if weight <= capacity_left:
                dp[i][capacity_left] = max(
                    dp[i][capacity_left],
                    value + dp[i - 1][capacity_left - weight]
                )

    return dp[n][capacity]
```

### 0/1 Knapsack - 1D

```python
def knapsack(weights, values, capacity):
    dp = [0] * (capacity + 1)

    for weight, value in zip(weights, values):
        for c in range(capacity, weight - 1, -1):
            dp[c] = max(dp[c], value + dp[c - weight])

    return dp[capacity]
```

## Dynamic Programming (Subsequence / String)

```python
def longest_common_subsequence(a, b):
    rows, cols = len(a), len(b)
    dp = [[0] * (cols + 1) for _ in range(rows + 1)]

    for i in range(1, rows + 1):
        for j in range(1, cols + 1):
            if a[i - 1] == b[j - 1]:
                dp[i][j] = dp[i - 1][j - 1] + 1
            else:
                dp[i][j] = max(
                    dp[i - 1][j],
                    dp[i][j - 1]
                )

    return dp[rows][cols]
```

### Longest Increasing Subsequence

```python
def length_of_lis(nums):
    dp = [1] * len(nums)

    for i in range(len(nums)):
        for j in range(i):
            if nums[j] < nums[i]:
                dp[i] = max(dp[i], dp[j] + 1)

    return max(dp, default=0)
```

## Dynamic Programming (State Machine)

```python
def max_profit(prices):
    hold = float("-inf")
    cash = 0

    for price in prices:
        hold = max(hold, cash - price)
        cash = max(cash, hold + price)

    return cash
```

### DP with Multiple States

```python
def solve(nums):
    prev = 0
    curr = 0

    for value in nums:
        new_curr = max(
            curr,
            prev + value
        )

        prev = curr
        curr = new_curr

    return curr
```

## Greedy

```python
def max_profit(prices):
    min_price = float("inf")
    max_profit = 0

    for price in prices:
        min_price = min(min_price, price)
        max_profit = max(max_profit, price - min_price)

    return max_profit
```

### Activity Selection

```python
def max_activities(intervals):
    intervals.sort(key=lambda x: x[1])

    count = 0
    end = float("-inf")

    for start, finish in intervals:
        if start >= end:
            count += 1
            end = finish

    return count
```

### Greedy with Sorting

```python
def assign_cookies(children, cookies):
    children.sort()
    cookies.sort()

    i = j = 0

    while i < len(children) and j < len(cookies):
        if cookies[j] >= children[i]:
            i += 1
        j += 1

    return i
```

## BFS / DFS (Graph Traversal)

```python
from collections import deque

def bfs(graph, start):
    visited = {start}
    queue = deque([start])
    order = []

    while queue:
        node = queue.popleft()
        order.append(node)

        for neighbor in graph[node]:
            if neighbor not in visited:
                visited.add(neighbor)
                queue.append(neighbor)

    return order


def dfs(graph, start, visited=None):
    if visited is None:
        visited = set()

    visited.add(start)

    for neighbor in graph[start]:
        if neighbor not in visited:
            dfs(graph, neighbor, visited)

    return visited
```

### BFS Shortest Path in Unweighted Graph

```python
from collections import deque

def shortest_path(graph, start, target):
    queue = deque([(start, 0)])
    visited = {start}

    while queue:
        node, distance = queue.popleft()

        if node == target:
            return distance

        for neighbor in graph[node]:
            if neighbor not in visited:
                visited.add(neighbor)
                queue.append((neighbor, distance + 1))

    return -1
```

### DFS Connected Components

```python
def count_components(graph):
    visited = set()
    count = 0

    def dfs(node):
        visited.add(node)

        for neighbor in graph[node]:
            if neighbor not in visited:
                dfs(neighbor)

    for node in graph:
        if node not in visited:
            dfs(node)
            count += 1

    return count
```

## Tree Traversal

```python
def preorder(root):
    if not root:
        return []

    return (
        [root.val] +
        preorder(root.left) +
        preorder(root.right)
    )


def inorder(root):
    if not root:
        return []

    return (
        inorder(root.left) +
        [root.val] +
        inorder(root.right)
    )


def postorder(root):
    if not root:
        return []

    return (
        postorder(root.left) +
        postorder(root.right) +
        [root.val]
    )
```

### Iterative Inorder

```python
def inorder(root):
    stack = []
    result = []
    current = root

    while stack or current:
        while current:
            stack.append(current)
            current = current.left

        current = stack.pop()
        result.append(current.val)
        current = current.right

    return result
```

### Level Order Traversal

```python
from collections import deque

def level_order(root):
    if not root:
        return []

    queue = deque([root])
    result = []

    while queue:
        level = []

        for _ in range(len(queue)):
            node = queue.popleft()
            level.append(node.val)

            if node.left:
                queue.append(node.left)

            if node.right:
                queue.append(node.right)

        result.append(level)

    return result
```

## Prefix Sum

```python
def build_prefix_sum(nums):
    prefix = [0] * (len(nums) + 1)

    for i, value in enumerate(nums):
        prefix[i + 1] = prefix[i] + value

    return prefix
```

### Range Sum Query

```python
def range_sum(prefix, left, right):
    return prefix[right + 1] - prefix[left]
```

### Subarray Sum Equals K

```python
def subarray_sum(nums, k):
    prefix_count = {0: 1}
    prefix = 0
    result = 0

    for num in nums:
        prefix += num

        result += prefix_count.get(prefix - k, 0)

        prefix_count[prefix] = prefix_count.get(prefix, 0) + 1

    return result
```

## Difference Array

```python
def apply_range_updates(n, updates):
    diff = [0] * (n + 1)

    for left, right, value in updates:
        diff[left] += value
        diff[right + 1] -= value

    result = [0] * n
    current = 0

    for i in range(n):
        current += diff[i]
        result[i] = current

    return result
```

## Hash Map / Frequency Counting

```python
from collections import Counter

def frequency_count(nums):
    return Counter(nums)
```

### Two Sum

```python
def two_sum(nums, target):
    seen = {}

    for i, num in enumerate(nums):
        complement = target - num

        if complement in seen:
            return [seen[complement], i]

        seen[num] = i

    return []
```

### Group Anagrams

```python
from collections import defaultdict

def group_anagrams(words):
    groups = defaultdict(list)

    for word in words:
        key = tuple(sorted(word))
        groups[key].append(word)

    return list(groups.values())
```

## Stack

```python
def is_valid_parentheses(s):
    stack = []
    pairs = {
        ")": "(",
        "]": "[",
        "}": "{"
    }

    for ch in s:
        if ch in "([{":
            stack.append(ch)
        else:
            if not stack or stack.pop() != pairs[ch]:
                return False

    return not stack
```

### Monotonic Stack

```python
def next_greater_element(nums):
    result = [-1] * len(nums)
    stack = []

    for i, num in enumerate(nums):
        while stack and nums[stack[-1]] < num:
            index = stack.pop()
            result[index] = num

        stack.append(i)

    return result
```

### Daily Temperatures Pattern

```python
def daily_temperatures(temperatures):
    result = [0] * len(temperatures)
    stack = []

    for i, temperature in enumerate(temperatures):
        while stack and temperature > temperatures[stack[-1]]:
            previous = stack.pop()
            result[previous] = i - previous

        stack.append(i)

    return result
```

### Monotonic Increasing Stack

```python
def largest_rectangle(heights):
    stack = []
    max_area = 0

    for i, height in enumerate(heights + [0]):
        while stack and heights[stack[-1]] > height:
            h = heights[stack.pop()]
            left = stack[-1] + 1 if stack else 0
            width = i - left
            max_area = max(max_area, h * width)

        stack.append(i)

    return max_area
```

## Heap / Priority Queue

```python
import heapq

def kth_smallest(nums, k):
    heap = nums[:]
    heapq.heapify(heap)

    for _ in range(k - 1):
        heapq.heappop(heap)

    return heapq.heappop(heap)
```

### Top K Elements

```python
import heapq

def top_k(nums, k):
    return heapq.nlargest(k, nums)
```

### Kth Largest Using Min Heap

```python
import heapq

def kth_largest(nums, k):
    heap = []

    for num in nums:
        heapq.heappush(heap, num)

        if len(heap) > k:
            heapq.heappop(heap)

    return heap[0]
```

### Merge K Sorted Lists

```python
import heapq

def merge_k_sorted(lists):
    heap = []

    for i, arr in enumerate(lists):
        if arr:
            heapq.heappush(heap, (arr[0], i, 0))

    result = []

    while heap:
        value, list_index, element_index = heapq.heappop(heap)
        result.append(value)

        next_index = element_index + 1

        if next_index < len(lists[list_index]):
            next_value = lists[list_index][next_index]
            heapq.heappush(
                heap,
                (next_value, list_index, next_index)
            )

    return result
```

## Intervals

### Merge Intervals

```python
def merge_intervals(intervals):
    intervals.sort(key=lambda x: x[0])

    result = []

    for start, end in intervals:
        if not result or start > result[-1][1]:
            result.append([start, end])
        else:
            result[-1][1] = max(result[-1][1], end)

    return result
```

### Insert Interval

```python
def insert_interval(intervals, new_interval):
    result = []
    i = 0

    while i < len(intervals) and intervals[i][1] < new_interval[0]:
        result.append(intervals[i])
        i += 1

    while i < len(intervals) and intervals[i][0] <= new_interval[1]:
        new_interval[0] = min(new_interval[0], intervals[i][0])
        new_interval[1] = max(new_interval[1], intervals[i][1])
        i += 1

    result.append(new_interval)
    result.extend(intervals[i:])

    return result
```

### Meeting Rooms

```python
def can_attend_meetings(intervals):
    intervals.sort()

    for i in range(1, len(intervals)):
        if intervals[i][0] < intervals[i - 1][1]:
            return False

    return True
```

## Linked List Manipulation

### Reverse Linked List

```python
def reverse_list(head):
    previous = None
    current = head

    while current:
        next_node = current.next
        current.next = previous
        previous = current
        current = next_node

    return previous
```

### Merge Two Sorted Lists

```python
def merge_lists(a, b):
    dummy = ListNode(0)
    current = dummy

    while a and b:
        if a.val <= b.val:
            current.next = a
            a = a.next
        else:
            current.next = b
            b = b.next

        current = current.next

    current.next = a or b

    return dummy.next
```

### Remove Nth Node From End

```python
def remove_nth_from_end(head, n):
    dummy = ListNode(0)
    dummy.next = head

    slow = fast = dummy

    for _ in range(n):
        fast = fast.next

    while fast.next:
        slow = slow.next
        fast = fast.next

    slow.next = slow.next.next

    return dummy.next
```

## Matrix Traversal

### Spiral Matrix

```python
def spiral_order(matrix):
    result = []

    if not matrix:
        return result

    top, bottom = 0, len(matrix) - 1
    left, right = 0, len(matrix[0]) - 1

    while top <= bottom and left <= right:
        for col in range(left, right + 1):
            result.append(matrix[top][col])

        top += 1

        for row in range(top, bottom + 1):
            result.append(matrix[row][right])

        right -= 1

        if top <= bottom:
            for col in range(right, left - 1, -1):
                result.append(matrix[bottom][col])

            bottom -= 1

        if left <= right:
            for row in range(bottom, top - 1, -1):
                result.append(matrix[row][left])

            left += 1

    return result
```

### Grid Directions

```python
DIRECTIONS = [
    (1, 0),
    (-1, 0),
    (0, 1),
    (0, -1)
]

for dr, dc in DIRECTIONS:
    nr = row + dr
    nc = col + dc
```

## Kadane's Algorithm

```python
def max_subarray(nums):
    current = nums[0]
    best = nums[0]

    for num in nums[1:]:
        current = max(num, current + num)
        best = max(best, current)

    return best
```

### Maximum Subarray with Indices

```python
def max_subarray_range(nums):
    current = nums[0]
    best = nums[0]

    start = 0
    best_start = 0
    best_end = 0

    for i in range(1, len(nums)):
        if nums[i] > current + nums[i]:
            current = nums[i]
            start = i
        else:
            current += nums[i]

        if current > best:
            best = current
            best_start = start
            best_end = i

    return best, best_start, best_end
```

## Union-Find / Disjoint Set Union

```python
class UnionFind:
    def __init__(self, n):
        self.parent = list(range(n))
        self.rank = [0] * n

    def find(self, x):
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])

        return self.parent[x]

    def union(self, a, b):
        root_a = self.find(a)
        root_b = self.find(b)

        if root_a == root_b:
            return False

        if self.rank[root_a] < self.rank[root_b]:
            root_a, root_b = root_b, root_a

        self.parent[root_b] = root_a

        if self.rank[root_a] == self.rank[root_b]:
            self.rank[root_a] += 1

        return True
```

### Detect Cycle in Undirected Graph

```python
def has_cycle(n, edges):
    uf = UnionFind(n)

    for a, b in edges:
        if not uf.union(a, b):
            return True

    return False
```

## Topological Sort

### Kahn's Algorithm

```python
from collections import deque

def topological_sort(n, edges):
    graph = [[] for _ in range(n)]
    indegree = [0] * n

    for a, b in edges:
        graph[a].append(b)
        indegree[b] += 1

    queue = deque(
        node for node in range(n)
        if indegree[node] == 0
    )

    order = []

    while queue:
        node = queue.popleft()
        order.append(node)

        for neighbor in graph[node]:
            indegree[neighbor] -= 1

            if indegree[neighbor] == 0:
                queue.append(neighbor)

    return order if len(order) == n else []
```

### Topological Sort with DFS

```python
def topological_sort(graph):
    state = {}
    order = []

    def dfs(node):
        if state.get(node) == 1:
            return False

        if state.get(node) == 2:
            return True

        state[node] = 1

        for neighbor in graph[node]:
            if not dfs(neighbor):
                return False

        state[node] = 2
        order.append(node)

        return True

    for node in graph:
        if not dfs(node):
            return []

    return order[::-1]
```

## Dijkstra's Algorithm

```python
import heapq

def dijkstra(graph, start):
    distances = {
        node: float("inf")
        for node in graph
    }

    distances[start] = 0
    heap = [(0, start)]

    while heap:
        distance, node = heapq.heappop(heap)

        if distance > distances[node]:
            continue

        for neighbor, weight in graph[node]:
            new_distance = distance + weight

            if new_distance < distances[neighbor]:
                distances[neighbor] = new_distance
                heapq.heappush(
                    heap,
                    (new_distance, neighbor)
                )

    return distances
```

## Trie

```python
class TrieNode:
    def __init__(self):
        self.children = {}
        self.is_word = False


class Trie:
    def __init__(self):
        self.root = TrieNode()

    def insert(self, word):
        node = self.root

        for ch in word:
            if ch not in node.children:
                node.children[ch] = TrieNode()

            node = node.children[ch]

        node.is_word = True

    def search(self, word):
        node = self.root

        for ch in word:
            if ch not in node.children:
                return False

            node = node.children[ch]

        return node.is_word

    def starts_with(self, prefix):
        node = self.root

        for ch in prefix:
            if ch not in node.children:
                return False

            node = node.children[ch]

        return True
```

## Bit Manipulation

### Check Bit

```python
def is_bit_set(num, position):
    return (num & (1 << position)) != 0
```

### Set Bit

```python
def set_bit(num, position):
    return num | (1 << position)
```

### Clear Bit

```python
def clear_bit(num, position):
    return num & ~(1 << position)
```

### Toggle Bit

```python
def toggle_bit(num, position):
    return num ^ (1 << position)
```

### Count Set Bits

```python
def count_bits(n):
    count = 0

    while n:
        n &= n - 1
        count += 1

    return count
```

### Find Unique Number

```python
def single_number(nums):
    result = 0

    for num in nums:
        result ^= num

    return result
```

## Bitmask / Subset Enumeration

```python
def generate_subsets(nums):
    n = len(nums)
    result = []

    for mask in range(1 << n):
        subset = []

        for i in range(n):
            if mask & (1 << i):
                subset.append(nums[i])

        result.append(subset)

    return result
```

### Enumerate Set Bits

```python
def set_bit_positions(mask):
    positions = []
    position = 0

    while mask:
        if mask & 1:
            positions.append(position)

        mask >>= 1
        position += 1

    return positions
```

## Divide and Conquer

```python
def merge_sort(arr):
    if len(arr) <= 1:
        return arr

    mid = len(arr) // 2

    left = merge_sort(arr[:mid])
    right = merge_sort(arr[mid:])

    result = []
    i = j = 0

    while i < len(left) and j < len(right):
        if left[i] < right[j]:
            result.append(left[i])
            i += 1
        else:
            result.append(right[j])
            j += 1

    return result + left[i:] + right[j:]
```

### Quick Sort

```python
def quick_sort(nums):
    if len(nums) <= 1:
        return nums

    pivot = nums[-1]

    left = [x for x in nums[:-1] if x <= pivot]
    right = [x for x in nums[:-1] if x > pivot]

    return quick_sort(left) + [pivot] + quick_sort(right)
```

## Sorting + Custom Comparator

```python
def sort_by_second(intervals):
    intervals.sort(key=lambda x: x[1])
    return intervals
```

### Sort by Multiple Conditions

```python
def sort_items(items):
    items.sort(
        key=lambda x: (x[0], -x[1])
    )

    return items
```

## Counting / Bucket Pattern

```python
def frequency_sort(nums):
    from collections import Counter

    frequency = Counter(nums)

    buckets = [[] for _ in range(len(nums) + 1)]

    for num, count in frequency.items():
        buckets[count].append(num)

    result = []

    for count in range(len(buckets) - 1, 0, -1):
        for num in buckets[count]:
            result.extend([num] * count)

    return result
```

## String Manipulation

### Character Frequency

```python
from collections import Counter

def are_anagrams(a, b):
    return Counter(a) == Counter(b)
```

### Reverse Words

```python
def reverse_words(s):
    return " ".join(s.split()[::-1])
```

### String Compression

```python
def compress(s):
    result = []
    count = 1

    for i in range(1, len(s) + 1):
        if i < len(s) and s[i] == s[i - 1]:
            count += 1
        else:
            result.append(s[i - count])
            result.append(str(count))
            count = 1

    return "".join(result)
```

## Reservoir Sampling

```python
import random

def random_choice(stream):
    chosen = None

    for i, value in enumerate(stream, start=1):
        if random.randrange(i) == 0:
            chosen = value

    return chosen
```

## Fenwick Tree / Binary Indexed Tree

```python
class FenwickTree:
    def __init__(self, n):
        self.tree = [0] * (n + 1)

    def update(self, index, value):
        index += 1

        while index < len(self.tree):
            self.tree[index] += value
            index += index & -index

    def query(self, index):
        index += 1
        result = 0

        while index > 0:
            result += self.tree[index]
            index -= index & -index

        return result

    def range_sum(self, left, right):
        if left == 0:
            return self.query(right)

        return self.query(right) - self.query(left - 1)
```

## Segment Tree

```python
class SegmentTree:
    def __init__(self, nums):
        n = len(nums)
        self.n = n
        self.tree = [0] * (2 * n)

        for i in range(n):
            self.tree[n + i] = nums[i]

        for i in range(n - 1, 0, -1):
            self.tree[i] = (
                self.tree[2 * i] +
                self.tree[2 * i + 1]
            )

    def update(self, index, value):
        index += self.n
        self.tree[index] = value

        while index > 1:
            index //= 2

            self.tree[index] = (
                self.tree[2 * index] +
                self.tree[2 * index + 1]
            )

    def query(self, left, right):
        left += self.n
        right += self.n

        result = 0

        while left <= right:
            if left % 2 == 1:
                result += self.tree[left]
                left += 1

            if right % 2 == 0:
                result += self.tree[right]
                right -= 1

            left //= 2
            right //= 2

        return result
```

## Reservoir / Randomized Selection

```python
import random

def random_index(nums, target):
    answer = -1
    count = 0

    for i, num in enumerate(nums):
        if num == target:
            count += 1

            if random.randrange(count) == 0:
                answer = i

    return answer
```

## LRU Cache Pattern

```python
from collections import OrderedDict

class LRUCache:
    def __init__(self, capacity):
        self.capacity = capacity
        self.cache = OrderedDict()

    def get(self, key):
        if key not in self.cache:
            return -1

        self.cache.move_to_end(key)
        return self.cache[key]

    def put(self, key, value):
        if key in self.cache:
            self.cache.move_to_end(key)

        self.cache[key] = value

        if len(self.cache) > self.capacity:
            self.cache.popitem(last=False)
```

## Design / Simulation Pattern

```python
def simulate(operations):
    state = {}

    for operation in operations:
        command = operation[0]

        if command == "add":
            value = operation[1]
            state[value] = state.get(value, 0) + 1

        elif command == "remove":
            value = operation[1]

            if value in state:
                state[value] -= 1

                if state[value] == 0:
                    del state[value]

    return state
```

## Common Graph Representation

### Adjacency List

```python
def build_graph(n, edges):
    graph = [[] for _ in range(n)]

    for a, b in edges:
        graph[a].append(b)
        graph[b].append(a)

    return graph
```

### Weighted Graph

```python
def build_weighted_graph(n, edges):
    graph = [[] for _ in range(n)]

    for a, b, weight in edges:
        graph[a].append((b, weight))
        graph[b].append((a, weight))

    return graph
```

## Grid BFS / DFS

```python
from collections import deque

def grid_bfs(grid, start):
    rows, cols = len(grid), len(grid[0])
    queue = deque([start])
    visited = {start}

    directions = [
        (1, 0),
        (-1, 0),
        (0, 1),
        (0, -1)
    ]

    while queue:
        r, c = queue.popleft()

        for dr, dc in directions:
            nr, nc = r + dr, c + dc

            if (
                0 <= nr < rows and
                0 <= nc < cols and
                (nr, nc) not in visited
            ):
                visited.add((nr, nc))
                queue.append((nr, nc))

    return visited
```

## Recursion (Divide and Conquer)

```python
def merge_sort(arr):
    if len(arr) <= 1:
        return arr

    mid = len(arr) // 2

    left = merge_sort(arr[:mid])
    right = merge_sort(arr[mid:])

    result = []
    i = j = 0

    while i < len(left) and j < len(right):
        if left[i] < right[j]:
            result.append(left[i])
            i += 1
        else:
            result.append(right[j])
            j += 1

    return result + left[i:] + right[j:]
```

### Recursive Tree Pattern

```python
def solve_tree(root):
    if not root:
        return 0

    left = solve_tree(root.left)
    right = solve_tree(root.right)

    return 1 + max(left, right)
```

### Recursive Array Pattern

```python
def solve(nums, index):
    if index == len(nums):
        return 0

    current = nums[index]
    remaining = solve(nums, index + 1)

    return current + remaining
```

---

# Quick-Reference Cheat Sheet

```text
List     : ordered, mutable        -> []
Tuple    : ordered, immutable       -> ()
Set      : unordered, unique          -> {}
Dict     : key-value pairs             -> {key: value}

Mutable    : list, dict, set
Immutable  : int, float, str, tuple, frozenset
```
