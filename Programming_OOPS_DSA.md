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
  temp = n % 10
  n //= 10
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
print(5 > 3 and 2 < 4)         # True

# Walrus operator (assignment inside expression)
if (n := 10) > 5:
    print(n)   # 10
```

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
print(s[0:6])     # 'Progra'
print(s[::-1])     # reversed string 'gnimmargorP'
print(s[::2])       # every 2nd char
```

---

# Numbers and Math

```python
import math

abs(-5)             # 5
round(3.14159, 2)     # 3.14
pow(2, 10)             # 1024
math.sqrt(16)            # 4.0
math.ceil(4.1)             # 5
math.floor(4.9)              # 4
max(3, 7, 2), min(3, 7, 2)     # 7, 2
sum([1, 2, 3])                   # 6
divmod(9, 2)                       # (4, 1)
```

---

# Collections

## Lists

```python
lst = [3, 1, 4, 1, 5]

lst.append(9)          # add to end
lst.insert(0, 100)       # insert at index
lst.remove(1)              # remove first matching value
lst.pop()                    # remove & return last item
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

## Dynamic Programming (Tabulation)

```python
def climb_stairs(n):
    dp = [0] * (n + 1)
    dp[0], dp[1] = 1, 1
    for i in range(2, n + 1):
        dp[i] = dp[i-1] + dp[i-2]
    return dp[n]
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

## BFS / DFS (Graph Traversal)

```python
from collections import deque

def bfs(graph, start):
    visited, queue, order = {start}, deque([start]), []
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

## Recursion (Divide and Conquer)

```python
def merge_sort(arr):
    if len(arr) <= 1:
        return arr
    mid = len(arr) // 2
    left, right = merge_sort(arr[:mid]), merge_sort(arr[mid:])
    result, i, j = [], 0, 0
    while i < len(left) and j < len(right):
        if left[i] < right[j]:
            result.append(left[i]); i += 1
        else:
            result.append(right[j]); j += 1
    return result + left[i:] + right[j:]
```

---

# Data Structures and Algorithms (DSA)

## Arrays

```python
arr = [5, 2, 9, 1]
arr.sort()                 # in-place sort O(n log n)
print(arr[-1])               # last element
```

## Linked List

```python
class Node:
    def __init__(self, val):
        self.val = val
        self.next = None

class LinkedList:
    def __init__(self):
        self.head = None

    def append(self, val):
        node = Node(val)
        if not self.head:
            self.head = node
            return
        cur = self.head
        while cur.next:
            cur = cur.next
        cur.next = node

    def display(self):
        cur, vals = self.head, []
        while cur:
            vals.append(cur.val)
            cur = cur.next
        return vals
```

## Stack (LIFO)

```python
stack = []
stack.append(1)   # push
stack.append(2)
stack.pop()          # pop -> 2
```

## Queue (FIFO)

```python
from collections import deque
queue = deque()
queue.append(1)     # enqueue
queue.append(2)
queue.popleft()        # dequeue -> 1
```

## Hash Map (Dictionary as Hash Table)

```python
freq = {}
for ch in "programming":
    freq[ch] = freq.get(ch, 0) + 1
```

## Binary Tree

```python
class TreeNode:
    def __init__(self, val):
        self.val = val
        self.left = None
        self.right = None

def inorder(root, result=None):
    if result is None:
        result = []
    if root:
        inorder(root.left, result)
        result.append(root.val)
        inorder(root.right, result)
    return result
```

## Heap / Priority Queue

```python
import heapq
heap = [5, 1, 8, 3]
heapq.heapify(heap)       # O(n) min-heap
heapq.heappush(heap, 0)
heapq.heappop(heap)          # returns smallest element
```

## Sorting Algorithms

```python
def bubble_sort(arr):
    n = len(arr)
    for i in range(n):
        for j in range(n - i - 1):
            if arr[j] > arr[j + 1]:
                arr[j], arr[j + 1] = arr[j + 1], arr[j]
    return arr

def quick_sort(arr):
    if len(arr) <= 1:
        return arr
    pivot = arr[len(arr) // 2]
    left = [x for x in arr if x < pivot]
    mid = [x for x in arr if x == pivot]
    right = [x for x in arr if x > pivot]
    return quick_sort(left) + mid + quick_sort(right)
```

## Searching Algorithms

```python
def linear_search(arr, target):
    for i, val in enumerate(arr):
        if val == target:
            return i
    return -1
# Binary search shown earlier in Coding Patterns
```

## Time & Space Complexity (Big-O) Reference

```text
O(1)        constant       dict lookup, list index access
O(log n)    logarithmic     binary search
O(n)        linear            linear scan, single loop
O(n log n)  linearithmic       merge sort, quick sort (avg)
O(n^2)      quadratic            bubble sort, nested loops
O(2^n)      exponential            recursive fibonacci (naive)
```

---

# Modules Every Python Programmer Should Know

```python
import itertools
list(itertools.permutations([1, 2, 3]))
list(itertools.combinations([1, 2, 3], 2))

import functools
functools.reduce(lambda a, b: a + b, [1, 2, 3, 4])   # 10

from functools import lru_cache
@lru_cache(maxsize=None)
def slow_fib(n):
    return n if n <= 1 else slow_fib(n-1) + slow_fib(n-2)
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
