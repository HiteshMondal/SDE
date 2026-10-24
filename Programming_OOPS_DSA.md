# Programming Complete Guide (Python and Go)

A single-file reference covering how programs are written, run, and organized, the concepts every language shares, built-ins, coding patterns, tricks, DSA, and OOP — each with a working code example. Python is the main language; Go examples sit next to it wherever the topic matters. The DSA and OOP sections stay Python only.

---

# Getting Started

## Installing Python

Download the installer for your operating system from https://www.python.org/downloads/ and pick a currently supported 3.x version.

```text
Windows : run the installer and TICK "Add python.exe to PATH" on the first screen
          (the py launcher is installed too:  py --version)
macOS   : python.org installer, or:  brew install python
Linux   : usually preinstalled; otherwise
          sudo apt install python3 python3-pip python3-venv     (Debian/Ubuntu)
          sudo dnf install python3 python3-pip                  (Fedora/RHEL)
```

```bash
python --version        # on Linux/macOS the command is often python3
python3 --version
pip --version           # also shows WHICH Python this pip belongs to
```

On Linux and macOS, `python3` is the safe command name. Do not replace or upgrade the system Python (OS tools depend on it); install other versions with `pyenv`, `uv`, or `conda`, and keep each project in its own virtual environment (see Dependencies and Environments).

## Installing Go

Download the installer from https://go.dev/dl/ and run it (Windows/macOS), or on Linux unpack the tarball and add it to `PATH`.

```bash
# Linux (replace the file name with the version you downloaded)
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf go1.XX.X.linux-amd64.tar.gz
export PATH=$PATH:/usr/local/go/bin        # add this line to ~/.bashrc or ~/.zshrc

go version                                  # verify the install
go env GOPATH GOROOT                        # where Go keeps downloaded modules and the toolchain
```

Programs installed with `go install` land in `$(go env GOPATH)/bin`, so add that folder to `PATH` too. Go modules (go.mod) can also pin a minimum Go version, and newer Go versions can download the matching toolchain automatically.

## Where to Write Code

Python and Go source code is plain text. Any editor can write it; an IDE or a good editor adds autocomplete, error highlighting, a debugger, and test runners.

* Python files end in `.py`, Go files end in `.go`
* Save the file, then run it from a terminal (next sections)
* A real application is a folder of files, not one file: an entry point, modules/packages, tests, and dependency files

Where the code eventually runs depends on the application:

* A script run by hand or by a scheduler (cron, CI pipeline)
* A command-line tool (`argparse` in Python, `flag` or `cobra` in Go)
* A web service (Flask/FastAPI/Django behind gunicorn/uvicorn in Python, `net/http` in Go) inside a container or on a server
* A notebook (Jupyter) for data work and experiments
* A serverless function (AWS Lambda, Cloud Functions)

## Choosing an IDE or Editor

* VS Code — free, works for both languages (install the Python extension and the Go extension); the best all-round choice to start with
* PyCharm (Community is free) — full Python IDE, strongest for large Python projects, refactoring, and debugging
* GoLand — the matching full IDE for Go
* Jupyter / JupyterLab / Google Colab — notebooks for data analysis and learning, not for building applications
* IDLE — ships with Python, fine for the first day only
* Vim/Neovim/Emacs — powerful, but need plugins (language servers) to match an IDE

Whatever you pick, set it to use the interpreter of the project's virtual environment, so autocomplete and the debugger see the same libraries your code will run with.

## Your First Program

```python
# file: hello.py
def main():
    name = input("Your name: ")
    print(f"Hello, {name}!")

if __name__ == "__main__":
    main()
```

```go
// file: main.go
package main            // package main = this code builds an executable program

import "fmt"            // standard library package for printing and formatting

func main() {           // the entry point: runs first
    var name string
    fmt.Print("Your name: ")
    fmt.Scanln(&name)
    fmt.Printf("Hello, %s!\n", name)
}
```

## Running a Python File

```bash
python hello.py                 # run a file (python3 on Linux/macOS, py on Windows)
python hello.py arg1 arg2       # arguments arrive in sys.argv
python -m mypackage.main        # run a module inside a package (keeps package imports working)
python -c "print('hi')"         # run one line
python -i hello.py              # run the file, then stay in the interactive prompt (REPL)
python                          # start the REPL; leave with exit() or Ctrl+D
```

```bash
# Make a script directly executable on Linux/macOS
# First line of the file:   #!/usr/bin/env python3
chmod +x hello.py
./hello.py
```

The exit code tells the caller whether the program succeeded: `0` means success, anything else means failure (`sys.exit(1)`).

## Running a Go Program

```bash
mkdir hello && cd hello
go mod init example.com/hello    # creates go.mod (every Go project is a module)
go run .                          # compile to a temporary binary and run it
go build                          # produce a real executable: ./hello (hello.exe on Windows)
./hello
go build -o myapp ./cmd/server    # choose output name and which package to build
GOOS=linux GOARCH=amd64 go build  # cross-compile for another OS/CPU
go install                        # build and copy the binary into $GOPATH/bin
```

## How an Application Is Organized

```text
myapp/                         (Python)
├── .venv/                     # virtual environment, never committed
├── pyproject.toml             # project info + dependencies (or requirements.txt)
├── README.md
├── src/myapp/
│   ├── __init__.py
│   ├── __main__.py            # makes "python -m myapp" work
│   ├── cli.py                 # entry point: reads arguments, calls the logic
│   └── services.py            # the actual logic, importable and testable
└── tests/
    └── test_services.py

myapp/                         (Go)
├── go.mod                     # module name + dependency versions
├── go.sum                     # checksums of dependencies
├── cmd/myapp/main.go          # package main: thin entry point
├── internal/service/service.go # logic, importable only from inside this module
└── internal/service/service_test.go
```

Keep the entry point thin: read configuration, wire things together, call into modules that can be imported and tested without starting the whole program.

## How Source Code Becomes a Running Program

Source code is text a human writes. A CPU can only run machine code, so something must translate it. There are two classic strategies:

* Compiled: a compiler translates the whole program ahead of time into machine code. You run the resulting file. (Go, C, Rust)
* Interpreted: an interpreter reads the program and executes it while running. (the original idea behind Python, Ruby, JavaScript)

In practice the line is blurry, and it belongs to the implementation, not the language. CPython (the standard Python) first compiles source to bytecode and then a virtual machine interprets that bytecode. Go compiles straight to a native executable. Other runtimes (the JVM, JavaScript engines, PyPy) add a JIT compiler that turns hot bytecode into machine code while the program runs.

```text
Python:  hello.py  --compile-->  bytecode (.pyc)  --Python VM executes-->  CPU
Go:      main.go   --compile + link-->  machine-code executable  --OS runs it-->  CPU
```

### Source Code, Bytecode, Machine Code, Executable

| Term | What it is | In Python | In Go |
| :--- | :--- | :--- | :--- |
| Source code | Human-readable text | `hello.py` | `main.go` |
| Bytecode | Compact instructions for a virtual machine, same on every OS/CPU | `__pycache__/hello.cpython-3xx.pyc` | not used |
| Machine code | Instructions for one specific CPU (x86-64, ARM64) | only inside the interpreter itself | inside your binary |
| Executable | A file the OS can launch: machine code + metadata (`.exe`, ELF, Mach-O) | `python` is the executable; your `.py` is just its input | `./hello` is the executable |

Python compiles each imported module to bytecode and caches it in `__pycache__`, so the next start is faster. You can look at the bytecode with the `dis` module:

```python
import dis

def add(a, b):
    return a + b

dis.dis(add)
# Prints instructions such as: LOAD_FAST a, LOAD_FAST b, BINARY_OP (+), RETURN_VALUE
# (the exact instructions change between Python versions)
```

An executable Go binary is self-contained, so you copy one file to a server and run it. A Python program needs a Python installation plus its libraries on the target machine (or a container image, or a bundler such as PyInstaller).

### What the Runtime Does

The runtime is the code that runs alongside your program and does the work the language promises. Your code never calls most of it directly.

* Memory management: allocating objects, tracking who still uses them, garbage collection
* Executing code: the Python VM loop; in Go, native code plus the runtime's helpers
* Scheduling: Python threads (under the GIL); Go's scheduler that runs goroutines on OS threads
* Safety checks: type checks and exceptions in Python; bounds checks, nil checks, and panics in Go
* Built-in data structures: dict/list in Python, map/slice/channel in Go
* Importing and loading code: Python resolves imports at run time; Go resolves them at compile time

In Python the runtime is the interpreter. In Go the runtime is a library linked into every binary, which is one reason even a tiny Go program is a few megabytes.

## Kinds of Errors

### Syntax Errors

The code is not valid for the language. Python reports them when it compiles the file (before any line runs); Go reports them at compile time and refuses to build.

```python
# print("hello"        -> SyntaxError: '(' was never closed
# if x = 5:            -> SyntaxError: invalid syntax (use == for comparison)
```

```go
// x := 5      declared but not used  -> compile error: declared and not used
// import "os" never used            -> compile error: "os" imported and not used
// var n int = "hi"                  -> compile error: cannot use "hi" (untyped string constant) as int value
```

### Runtime Errors

The code is valid, but something goes wrong while it runs: bad input, missing file, network down, bug in a path nobody tested.

```python
nums = [1, 2, 3]
# nums[10]        -> IndexError
# int("abc")      -> ValueError
# 10 / 0          -> ZeroDivisionError
# None.upper()    -> AttributeError
```

```go
nums := []int{1, 2, 3}
// nums[10]                  -> panic: runtime error: index out of range [10] with length 3
// var m map[string]int; m["a"] = 1  -> panic: assignment to entry in nil map
// var p *int; *p = 1                -> panic: nil pointer dereference
```

Python raises exceptions that can be caught. Go separates expected failures (returned as `error` values) from bugs (`panic`). See Exception Handling.

### Logical Errors

The program runs without any error and gives the wrong answer. No tool can report them for you. You find them with tests, print/log statements, and a debugger.

```python
def average(nums):
    return sum(nums) / len(nums) + 1     # bug: no error, wrong result

print(average([2, 4, 6]))                # 5.0, expected 4.0
```

Static typing (Go) turns whole categories of runtime errors into compile errors. Python can get part of that with type hints and a checker such as `mypy`.

## Reading an Error Message

Read a Python traceback from the bottom up: the last line is the error, the line above it is where it happened, and the lines above that are who called it.

```text
Traceback (most recent call last):
  File "app.py", line 9, in <module>
    main()
  File "app.py", line 6, in main
    return parse_age("abc")
  File "app.py", line 2, in parse_age
    return int(text)
ValueError: invalid literal for int() with base 10: 'abc'
```

A Go panic prints the message first, then the stack of the goroutine that panicked:

```text
panic: runtime error: index out of range [5] with length 3

goroutine 1 [running]:
main.main()
    /home/me/app/main.go:9 +0x1d
exit status 2
```

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

## Variables and Data Types in Go

Go is statically typed: every variable has one fixed type, known at compile time. Code lives in packages and starts at `func main()`.

```go
package main

import "fmt"

func main() {
    var name string = "Alice"   // string
    age := 25                    // int (type inferred)
    height := 5.6                // float64 (inferred)
    isActive := true             // bool
    var data *int                // nil pointer: Go's closest thing to None for pointers

    fmt.Printf("%T %T %T %T\n", name, age, height, isActive)   // string int float64 bool
    fmt.Println(data == nil)                                    // true
}
```

### var vs :=

```go
var x int            // declares x with its zero value (0); the type is written out
var y int = 10       // type and value
var z = 10           // type inferred from the value (int)
w := 10              // short declaration: declare + infer in one step

var a, b = 1, "two"  // several at once
c, d := 3, 4.5

var (                // grouped declaration, common at package level
    host = "localhost"
    port = 8080
)

// := needs at least one NEW variable on the left side
n, err := strconv.Atoi("1")
m, err := strconv.Atoi("2")    // fine: m is new, err is reused (assigned, not redeclared)
```

* `var x int` works anywhere (including package level) and gives the zero value; `x := 10` works only inside functions and always infers the type
* Use `var` when you want the zero value or must state the type (`var f float64 = 1`), use `:=` for everything else
* An unused local variable or an unused import is a compile error; assign to `_` to ignore a value on purpose

### Zero Values (Default Values)

Go has no uninitialized variables. Every variable that is declared without a value gets its type's zero value.

```go
var i int              // 0
var f float64          // 0
var b bool             // false
var s string           // "" (an empty string, never nil)
var p *int             // nil
var sl []int           // nil slice (len 0, cap 0)
var m map[string]int   // nil map
var ch chan int        // nil channel
var fn func()          // nil
var e error            // nil (interface)
var arr [3]int         // [0 0 0]

type Point struct {
    X, Y int
    Name string
}
var pt Point           // {0 0 ""}: every field gets its own zero value
```

* The zero value of a slice is a nil slice: `len` is 0, and `append` and `range` work on it
* The zero value of a map is a nil map: reading returns the zero value of the element type, but writing panics (use `make`)
* The zero value of a pointer is `nil`; dereferencing it panics
* The zero value of a struct is a struct with all fields zeroed, and it is a usable value; good Go types are designed so the zero value works (`sync.Mutex`, `bytes.Buffer`, `strings.Builder`)

In Python a name that was never assigned simply does not exist (`NameError`), and "no value" is expressed explicitly with `None`.

### Constants and iota

```go
const Pi = 3.14159
const (
    Sunday = iota    // 0
    Monday           // 1
    Tuesday          // 2
)
```

Python has no real constants; the convention is an UPPER_CASE name (`MAX_RETRIES = 3`) that nobody should reassign.

### Basic Types

```go
bool
string
int, int8, int16, int32, int64         // int is 32 or 64 bits depending on the platform (64 on modern machines)
uint, uint8, uint16, uint32, uint64
float32, float64
byte                                   // alias for uint8
rune                                   // alias for int32: one Unicode code point
complex64, complex128
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

## F-strings

F-strings (formatted string literals), introduced in Python 3.6, are the modern, standard, and most efficient way to format strings in Python. They are faster and more readable than older formatting methods like % formatting or .format().

```python
name = "Alice"
age = 30

# Basic variable insertion
print(f"My name is {name} and I am {age} years old.")
# Output: My name is Alice and I am 30 years old.

# Math operations and string methods inside braces
print(f"Next year, you will be {age + 1}. Your name in uppercase is {name.upper()}.")
# Output: Next year, you will be 31. Your name in uppercase is ALICE.
```

| Formatting Goal | Syntax | Example | Output |
| :--- | :--- | :--- | :--- |
| **Float Precision** | `:.Nf` | `f"{3.14159:.2f}"` | `3.14` |
| **Thousands Separator** | `:,` | `f"{1000000:,}"` | `1,000,000` |
| **Percentage** | `:.N%` | `f"{0.75:.1%}"` | `75.0%` |
| **Zero Padding** | `:0N` | `f"{7:03}"` | `007` |
| **String Padding/Width** | `:N` | `f"{'hi':5}!"` | `'hi   !'` |

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

## Type Conversion in Go

Go never converts between different types on its own. Even `int` to `int64` or `int` to `float64` needs an explicit conversion written as `T(value)`. Converting text to numbers and back goes through the `strconv` package, and parsing returns an `error` because it can fail.

```go
i := 42
f := float64(i)           // 42   explicit int -> float64
g := 3.99
n := int(g)               // 3    truncates toward zero, does not round
var small int8 = int8(300) // overflow wraps silently when converting variables (value changes!)

// number <-> string
s := strconv.Itoa(i)                    // "42"
n, err := strconv.Atoi("42")            // 42, nil
n, err = strconv.Atoi("abc")            // 0, error: invalid syntax
f, err := strconv.ParseFloat("3.14", 64) // 3.14
b, err := strconv.ParseBool("true")      // true
x, err := strconv.ParseInt("ff", 16, 64) // 255   (string, base, bit size)
bin := strconv.FormatInt(10, 2)          // "1010"
t := fmt.Sprint(i)                       // "42"   (works for any value)

// string <-> bytes <-> runes
bs := []byte("abc")        // [97 98 99]
rs := []rune("héllo")      // one rune per character
str := string(rs)          // back to string

string(rune(65))           // "A"   int -> character
string(65)                 // also "A", NOT "65" (go vet warns); use strconv.Itoa(65) for "65"

// bool has no numeric conversion: if flag { n = 1 } else { n = 0 }
```

### Mixing Types Does Not Compile

```go
var a int = 1
var b int64 = 2
// c := a + b           // compile error: mismatched types int and int64
c := int64(a) + b       // convert one side explicitly

const k = 10            // untyped constant: adapts to the type it meets
var d float64 = k * 1.5 // fine
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

## Operators in Go

```go
7 / 2           // 3     integer division when both sides are ints (truncates toward zero)
7.0 / 2         // 3.5   float division if either side is a float
-7 / 2          // -3    Python gives -4 (// floors); Go truncates toward zero
-7 % 3          // -1    Python gives 2 (result takes the sign of the divisor); Go takes the sign of the dividend
// no ** operator: use math.Pow(2, 10), which returns float64

a, b := 5, 3
a & b           // 1     AND
a | b           // 7     OR
a ^ b           // 6     XOR (binary)
^a              // -6    NOT is ^ in Go, not ~
a &^ b          // 4     AND NOT: clears the bits of a that are set in b
a << 1          // 10
a >> 1          // 2

x := 5
x++             // increment is a statement, not an expression: no y := x++, no ++x
x += 2

// && || ! for logic (not "and", "or", "not")
// no ternary operator and no walrus operator: use a normal if statement
```

---

# Type Systems

## Static vs Dynamic Typing

The type system decides when a value's type is checked.

* Dynamic typing (Python): types belong to values, not to names. A name can be rebound to any type, and type mistakes are found only when the offending line actually runs.
* Static typing (Go): types belong to variables and expressions, are fixed, and are checked by the compiler before the program ever runs.

```python
x = 10
x = "ten"            # allowed: the name x now points to a str

def add(a, b):
    return a + b

add(1, 2)            # 3
add("a", "b")        # "ab"   same function, different types (duck typing)
add(1, "b")          # TypeError, discovered only when this line executes
```

```go
var x int = 10
// x = "ten"          // compile error: cannot use "ten" (untyped string constant) as int value in assignment

func add(a, b int) int { return a + b }
// add("a", "b")      // compile error: cannot use "a" as int value in argument to add
```

Trade-off: dynamic typing is quick to write and flexible; static typing finds a whole class of bugs before shipping, makes refactoring safer, and lets tools autocomplete reliably, at the cost of more up-front declarations.

## Type Inference

Inference means the compiler works out a type from the value, so you do not write it. It is not the same as dynamic typing: the type is still fixed at compile time.

```go
count := 10             // int
price := 9.99           // float64
name := "Go"            // string
ok := true              // bool
ch := 'a'               // rune (int32)
items := []string{}     // []string
```

Inference in Go stops at function boundaries: parameters and return types must be written. Untyped constants get default types: `int`, `float64`, `rune`, `string`, `bool`.

Python never declares types at all. Type hints are optional documentation plus input for checkers such as `mypy` or `pyright`; Python itself ignores them at runtime:

```python
def double(n: int) -> int:
    return n * 2

double("ab")        # runs fine and returns "abab"; mypy would flag it before you run it
```

## Strong vs Weak Typing

This is a different question from static vs dynamic: how willing is the language to silently convert between unrelated types?

* Strong: mixing incompatible types is an error. Python is strong but dynamic. Go is strong and static.
* Weak: the language converts silently and keeps going, which hides bugs.

```python
"1" + 1            # TypeError: can only concatenate str (not "int") to str
"1" + str(1)       # "11"   you must say what you mean
```

```javascript
// JavaScript (weakly typed), shown only for contrast
"1" + 1            // "11"   number silently turned into a string
"5" - 2            // 3      string silently turned into a number
```

|  | Dynamic | Static |
| :--- | :--- | :--- |
| Strong | Python | Go |
| Weak | JavaScript, PHP | C (and shell scripts treat everything as strings) |

## Explicit vs Implicit Conversion

Implicit conversion (coercion) happens automatically; explicit conversion is written by you.

```python
1 + 2.5            # 3.5    int -> float automatically
True + 1           # 2      bool is a subclass of int
if []:             # empty list counts as False: truthiness is an implicit conversion
    pass
print(42)          # print() calls str() for you

int("5")           # explicit: strings are never converted to numbers automatically
```

Go has almost no implicit conversion. The exceptions are untyped constants adapting to their context, and a concrete value being assigned to an interface it satisfies. Everything else is `T(value)` (see Type Conversion in Go). Prefer explicit conversions: the reader sees exactly where a value changes type and where data may be lost (for example `float → int` truncation or overflow when narrowing).

## None, nil, and NULL

Each language needs a way to say "no value here". The name and the exact meaning differ:

| Language | Name | What it is |
| :--- | :--- | :--- |
| Python | `None` | A single object of type `NoneType`; compare with `is None` |
| Go | `nil` | The zero value of pointers, slices, maps, channels, functions, and interfaces; not available for `int`, `string`, `bool`, or structs |
| C, C++, Java | `NULL` / `null` | A pointer or reference that points to nothing; using it crashes or throws |
| SQL | `NULL` | "Unknown or missing"; `NULL = NULL` is not true, you must write `IS NULL` |

```python
result = None
if result is None:               # use "is", not "=="
    print("nothing yet")

def no_return():
    pass
print(no_return())               # None: functions without return give back None

# Classic bug
user = None
# user.name                      # AttributeError: 'NoneType' object has no attribute 'name'
```

```go
var p *int                       // nil
var s []int                      // nil slice: safe to len(), range, and append
var m map[string]int             // nil map: safe to read, panics on write
// *p                            // panic: nil pointer dereference

// Famous gotcha: a nil pointer stored in an interface is NOT a nil interface
func find() error {
    var e *MyError = nil
    return e                     // the interface holds (type *MyError, value nil)
}
fmt.Println(find() == nil)       // false!  return a literal nil instead
```

Always decide what "missing" means for each value: return an `error` or `(value, ok)` in Go, return `None` or raise an exception in Python, and check before you use it.

## Type Errors

A type error means an operation was applied to a value of the wrong type.

* Python reports it at run time as `TypeError` (or `AttributeError`, `ValueError`) on the line that executes
* Go reports almost all of them at compile time; the ones left are type assertions (`i.(string)` panics when the dynamic type is different) and nil dereferences

```python
len(5)                    # TypeError: object of type 'int' has no len()
"abc" * "d"               # TypeError: can't multiply sequence by non-int of type 'str'
int([1, 2])               # TypeError: int() argument must be a string, a bytes-like object or a real number
```

```go
var v any = "hello"
s := v.(string)           // ok
n := v.(int)              // panic: interface conversion: interface {} is string, not int
n, ok := v.(int)          // safe form: ok is false, no panic
```

Habit that prevents most type errors in Python: add type hints, run `mypy` or `pyright` in CI, and validate data at the program's edges (user input, files, APIs).

---

# Conditionals

## if, elif, else in Python

```python
age = 20

if age < 13:
    print("child")
elif age < 18:
    print("teen")
else:
    print("adult")

# Truthiness: falsy values are None, False, 0, 0.0, "", [], (), {}, set()
items = []
if not items:
    print("empty")

# match statement (Python 3.10+): pattern matching
command = "stop"
match command:
    case "start":
        print("starting")
    case "stop" | "halt":
        print("stopping")
    case [x, y]:                 # matches any 2-item sequence
        print(x, y)
    case _:                      # default
        print("unknown")
```

## if and switch in Go

```go
age := 20

if age < 13 {                    // no parentheses; braces are mandatory
    fmt.Println("child")
} else if age < 18 {
    fmt.Println("teen")
} else {
    fmt.Println("adult")
}

// init statement: n is scoped to the whole if/else chain
if n, err := strconv.Atoi("42"); err != nil {
    fmt.Println("bad number:", err)
} else if n > 40 {
    fmt.Println("big", n)
}

// The condition must be a bool: there is no truthiness in Go
items := []int{}
// if items { }                  // compile error
if len(items) == 0 {
    fmt.Println("empty")
}

// switch: no break needed, a case never falls into the next one
day := "sat"
switch day {
case "sat", "sun":
    fmt.Println("weekend")
case "fri":
    fmt.Println("almost")
    fallthrough                  // explicit opt-in to continue into the next case
default:
    fmt.Println("weekday")
}

// switch with no expression: a tidy if/else chain
switch {
case age < 13:
    fmt.Println("child")
case age < 18:
    fmt.Println("teen")
default:
    fmt.Println("adult")
}
```

Go has no ternary operator (`a if c else b` in Python); write a normal `if`.

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

## Loops in Go

Go has exactly one loop keyword, `for`, which covers every form: counting, while, infinite, and iterating.

```go
// classic three-part loop
for i := 0; i < 5; i++ {
    fmt.Println(i)                 // 0 1 2 3 4
}

// while loop: only the condition
n := 5
for n > 0 {
    n--
}

// infinite loop with controlled exit
count := 0
for {
    count++
    if count > 3 {
        break
    }
}

// range over an integer (Go 1.22+)
for i := range 3 {
    fmt.Println(i)                 // 0 1 2
}

// range over a slice: index and value
fruits := []string{"apple", "banana", "cherry"}
for index, fruit := range fruits {
    fmt.Println(index, fruit)
}
for _, fruit := range fruits {}    // _ discards the index
for i := range fruits {}           // index only

// range over a map: order is random on purpose
d := map[string]int{"a": 1, "b": 2}
for key, value := range d {
    fmt.Println(key, value)
}

// range over a string: yields (byte index, rune)
for i, r := range "héllo" {
    fmt.Println(i, string(r))
}

// continue and break, with a label to leave an outer loop
outer:
for i := 0; i < 3; i++ {
    for j := 0; j < 3; j++ {
        if j == 1 {
            continue outer         // next iteration of the OUTER loop
        }
        if i == 2 {
            break outer            // leave both loops
        }
        fmt.Println(i, j)
    }
}
```

* Go has no `while`, no `do-while`, no loop `else`, and no comprehensions; write the loop and append to a slice
* `range` copies the value each iteration, so changing `v` in `for _, v := range items` does not change the slice; use the index (`items[i] = ...`)
* Since Go 1.22 each iteration gets its own loop variable (this matters for closures and goroutines started inside the loop)

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

## Text, Bytes and Unicode in Python

A Python `str` is a sequence of Unicode characters (code points). Files, sockets, and APIs deal in `bytes`. Converting between them is encoding and decoding, and you should always name the encoding.

```python
s = "héllo"
len(s)                  # 5   characters
b = s.encode("utf-8")   # b'h\xc3\xa9llo'
len(b)                  # 6   bytes (é takes two bytes in UTF-8)
b.decode("utf-8")       # 'héllo'

s[0] = "H"              # TypeError: strings are immutable; build a new one: "H" + s[1:]

with open("data.txt", "r", encoding="utf-8") as f:   # always pass encoding for text files
    text = f.read()
```

## Strings in Go

A Go string is an immutable sequence of bytes, usually UTF-8 text. `len` counts bytes, not characters. A `rune` is one Unicode code point.

```go
s := "héllo"
len(s)                     // 6   bytes
len([]rune(s))             // 5   runes (characters)
utf8.RuneCountInString(s)  // 5   same, without allocating
s[1]                       // 195 a single BYTE, not a character
// s[0] = 'H'              // compile error: strings are immutable

for i, r := range s {      // range decodes UTF-8: i = byte offset, r = rune
    fmt.Println(i, string(r))
}

raw := `C:\path\file.txt
second line`               // backticks = raw multi-line string, no escapes
```

```go
import "strings"

strings.ToUpper("go")                // "GO"
strings.TrimSpace("  hi  ")          // "hi"
strings.Split("a,b,c", ",")          // ["a" "b" "c"]
strings.Fields("dog  cat")           // ["dog" "cat"]  (splits on any whitespace, like Python split())
strings.Join([]string{"a", "b"}, "-") // "a-b"
strings.Contains("hello", "ell")     // true
strings.HasPrefix("hello", "he")     // true
strings.Index("hello", "l")          // 2   (-1 if not found)
strings.Count("banana", "a")         // 3
strings.ReplaceAll("a-b-c", "-", "+") // "a+b+c"
strings.Repeat("ab", 3)              // "ababab"
```

```go
// Building a string in a loop: use strings.Builder, not += (each += copies the whole string)
var sb strings.Builder
for i := 0; i < 3; i++ {
    fmt.Fprintf(&sb, "%d,", i)
}
result := sb.String()                // "0,1,2,"
```

Formatting uses `fmt` verbs, the Go version of f-strings:

```go
name, age, price := "Bob", 30, 3.14159
fmt.Printf("%s is %d years old\n", name, age)   // Bob is 30 years old
fmt.Printf("%.2f\n", price)                      // 3.14
fmt.Printf("%08.3f|%5d|%-5d|\n", price, age, age) // 0003.142|   30|30   |
fmt.Printf("%v %+v %T\n", pt, pt, pt)            // value, value with field names, type
fmt.Printf("%q %x %b %c\n", "hi", 255, 5, 'A')   // "hi" ff 101 A
msg := fmt.Sprintf("%s-%d", name, age)           // build a string without printing
```

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

## Precision, Overflow and Rounding

Python integers have unlimited size, so they never overflow. Floats are IEEE 754 binary numbers, so many decimals cannot be stored exactly. Go integers have a fixed size and wrap around silently.

```python
2 ** 100                      # 1267650600228229401496703205376  (no overflow)
0.1 + 0.2                     # 0.30000000000000004
0.1 + 0.2 == 0.3              # False
math.isclose(0.1 + 0.2, 0.3)  # True   compare floats with a tolerance

from decimal import Decimal
Decimal("0.1") + Decimal("0.2") == Decimal("0.3")   # True   use Decimal for money

round(2.5), round(3.5)        # 2 4    rounds half to even ("banker's rounding")
-7 // 2, -7 % 3               # -4 2   // floors toward minus infinity, % takes the divisor's sign
float("nan") == float("nan")  # False  NaN is never equal to anything, even itself
```

```go
var u uint8 = 255
u++                            // 0     wraps around, no error
var i8 int8 = 127
i8++                           // -128  wraps around, no error
fmt.Println(math.MaxInt64)     // 9223372036854775807

a, b := 0.1, 0.2
fmt.Println(a + b)             // 0.30000000000000004  same IEEE 754 behavior
fmt.Println(0.1 + 0.2)         // 0.3   constants are computed exactly at compile time

zero := 0
// fmt.Println(10 / zero)      // panic: runtime error: integer divide by zero  (a literal "/ 0" is a compile error)
fzero := 0.0
fmt.Println(10 / fzero)        // +Inf  floats do not panic

// huge numbers: math/big
two := big.NewInt(2)
two.Exp(two, big.NewInt(100), nil)
fmt.Println(two)               // 1267650600228229401496703205376

math.Sqrt(16)                  // 4
math.Pow(2, 10)                // 1024
math.Floor(4.9)                // 4
math.Ceil(4.1)                 // 5
math.Round(2.5)                // 3   Go rounds half away from zero (Python rounds half to even)
math.Abs(-5)                   // 5
```

Money: never use floats. Store integer cents (`int64`), or use `Decimal` in Python.

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

# Updating elements
d["b"] = 20               # update existing key directly
d["e"] = 5                   # adding a new key directly (same syntax as update)

# Checking keys
"b" in d                  # True  -> checks keys only
"z" in d                     # False
"z" not in d                    # True

# Length
len(d)                    # number of key-value pairs

# Nested hashmaps
nested = {
    "user1": {"name": "Alice", "age": 25},
    "user2": {"name": "Bob", "age": 30}
}

nested["user1"]["name"]        # "Alice"
nested["user2"]["age"] = 31       # update nested value
nested["user3"] = {"name": "Cara", "age": 22}   # add new nested entry

for user_id, info in nested.items():
    print(user_id, info["name"], info["age"])

# Hash collisions
# Python dicts use hashing internally to store keys.
# Two different keys can (rarely) produce the same hash bucket internally,
# but Python handles this automatically -> you never manage collisions manually.
# You just need keys to be hashable (see below); Python's dict implementation
# resolves any internal collision transparently.

# Valid dict keys
# A key must be hashable (immutable types): str, int, float, bool, tuple (if its contents are also hashable)
valid = {
    "name": "Alice",      # str key
    1: "one",                # int key
    (1, 2): "point"             # tuple key (valid, since tuple is immutable)
}

# invalid = {[1,2]: "value"}   # TypeError: list is unhashable (mutable)
# invalid = {{1,2}: "value"}      # TypeError: set is unhashable (mutable)

# Dict comprehension
squares = {x: x*x for x in range(5)}
```

### Dictionary Using Tuples

```python
# Tuple as KEY: composite keys (coordinates, (city, year), (row, col))
grid = {(0, 0): "start", (2, 3): "wall"}
grid[(2, 3)]               # 'wall'
grid[2, 3]                 # same: the parentheses are optional
(5, 5) in grid             # False
grid.get((9, 9), ".")      # '.' default for a missing cell

sales = {("Delhi", 2025): 120, ("Delhi", 2026): 150}
for (city, year), amount in sales.items():     # unpack the key tuple in the loop
    print(city, year, amount)

# Tuple as VALUE
places = {"home": (28.6, 77.2), "office": (28.5, 77.3)}
lat, lon = places["home"]

# Build a dict from a tuple of pairs
dict((("a", 1), ("b", 2)))                     # {'a': 1, 'b': 2}
dict(zip(("x", "y"), (10, 20)))                # {'x': 10, 'y': 20}

# Sort by the tuple value
sorted(places.items(), key=lambda kv: kv[1])   # sorts by (lat, lon)
```

Rules and gotchas:

```python
{(1, [2]): "x"}            # TypeError: unhashable type: 'list'  (every item inside must be hashable)
{tuple([1, 2]): "x"}       # fine: convert a list to a tuple first
(1, 2) == (2, 1)           # False: order matters, so keys (1, 2) and (2, 1) are different
```

* A tuple key is a flat alternative to nested dicts: `sales[("Delhi", 2025)]` vs `sales["Delhi"][2025]`. Tuple keys are simpler to write and check; nested dicts are easier to loop over one level.
* Use `collections.namedtuple` as the key when you want readable fields: `Point(x=1, y=2)`.
* In Go the equivalent is an array or struct key: `map[[2]int]string` or `map[Point]string`.

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

## Arrays and Slices in Go

An array has a fixed length that is part of its type. A slice is a flexible view over an array and is what Go code uses almost everywhere (it plays the role of Python's list).

```go
var a [3]int              // [0 0 0]
b := [3]int{1, 2, 3}
c := [...]int{1, 2, 3, 4} // the compiler counts the elements
// [3]int and [4]int are different types, and arrays are values:
d := b                    // copies all three elements
d[0] = 99                 // b is unchanged
```

A slice is a small struct of three fields: a pointer to an underlying array, a length, and a capacity.

```go
s := []int{1, 2, 3}          // len 3, cap 3
t := make([]int, 3, 10)      // len 3, cap 10 (room to grow without reallocating)
fmt.Println(len(s), cap(s))  // 3 3

s = append(s, 4)             // ALWAYS assign the result of append
s = append(s, 5, 6)
s = append(s, t...)          // append another slice with ...
copy(t, s)                   // copy elements into an existing slice

sub := s[1:3]                // like Python slicing, but it SHARES the same underlying array
sub[0] = 100                 // s[1] is now 100 too
clone := slices.Clone(s)     // independent copy (Go 1.21+)

s = append(s[:1], s[2:]...)  // delete the element at index 1
s = slices.Delete(s, 1, 2)   // same idea, helper version

var nilSlice []int           // nil slice: len 0, but append and range work
empty := []int{}             // empty, non-nil slice; len 0 too
len(nilSlice) == 0           // check emptiness with len, not == nil
```

### What Happens When append Exceeds Capacity

If there is spare capacity, `append` writes into the same underlying array. When the length would exceed the capacity, Go allocates a new, larger array, copies the elements over, and returns a slice pointing at the new array. The old array is left untouched. Growth is roughly doubling for small slices and slower for large ones; the exact rule is an implementation detail.

```go
a := make([]int, 3, 3)       // len 3, cap 3 (full)
b := append(a, 4)            // exceeds capacity: new array allocated
b[0] = 100
fmt.Println(a[0])            // 0    a still points at the OLD array

c := make([]int, 3, 10)      // len 3, cap 10
d := append(c, 4)            // fits: same array reused
d[0] = 100
fmt.Println(c[0])            // 100  c and d share memory
```

Consequences: always write `s = append(s, x)`; do not assume two slices share (or do not share) memory after an append; use `slices.Clone` or `copy` when you need an independent copy; preallocate with `make([]T, 0, n)` when you know the size. A function that appends to a slice parameter changes only its local copy of the slice header, so return the new slice.

```go
func add(s []int) { s = append(s, 1) }       // caller does NOT see the new element
func add2(s []int) []int { return append(s, 1) } // caller does: nums = add2(nums)
```

## Maps in Go

```go
m := map[string]int{"a": 1, "b": 2}
m2 := make(map[string]int)   // empty, ready to use

m["c"] = 3                   // insert or update
v := m["z"]                  // 0: a missing key returns the zero value
v, ok := m["z"]              // comma-ok form: ok is false when the key is missing
delete(m, "a")               // no error if the key does not exist
len(m)

for k, v := range m {        // iteration order is random
    fmt.Println(k, v)
}

var nm map[string]int        // nil map
_ = nm["x"]                  // reading is fine
// nm["x"] = 1               // panic: assignment to entry in nil map (create it with make first)

// a set is a map with empty values
seen := map[string]struct{}{}
seen["go"] = struct{}{}
_, found := seen["go"]       // true
```

### Key Constraints

A map key type must be comparable, meaning `==` works on it: booleans, numbers, strings, pointers, channels, arrays of comparable types, and structs whose fields are all comparable. Slices, maps, and functions are not comparable, so they cannot be keys. This is the same idea as Python's "keys must be hashable".

Can a slice be a map key? No: `map[[]int]string` is a compile error (`invalid map key type []int`). Use an array (`[3]int`), a struct, or convert the slice to a string key.

```go
type Point struct{ X, Y int }
grid := map[Point]string{{1, 2}: "tree"}     // struct key: fine
visited := map[[2]int]bool{{0, 1}: true}     // array key: fine (like a tuple in Python)
// bad := map[[]int]string{}                 // compile error

key := fmt.Sprint([]int{1, 2, 3})            // "[1 2 3]": a string key built from a slice
```

### Reference-Like Behavior

A map variable holds a reference to the runtime's hash table. Assigning or passing a map copies only that reference, so every copy sees the same data.

```go
func rename(m map[string]int) { m["x"] = 99 }

m := map[string]int{"x": 1}
copyRef := m
rename(m)
fmt.Println(m["x"], copyRef["x"])     // 99 99

other := maps.Clone(m)                // real independent copy (maps package, Go 1.21+)
```

Maps are not safe for concurrent use: writing from two goroutines at once crashes the program (`fatal error: concurrent map writes`). Protect the map with a mutex, or use `sync.Map` for special cases. Struct values stored in a map are not addressable, so `m["k"].count++` does not compile; copy the value out, change it, and store it back.

---

# Enums

An enum is a fixed set of named constants. It replaces "magic" values like `status = 2` with readable, checkable names.

## Enums in Python

```python
from enum import Enum, IntEnum, StrEnum, Flag, auto, unique

class Color(Enum):
    RED = 1
    GREEN = 2
    BLUE = 3

Color.RED                 # <Color.RED: 1>
Color.RED.name            # 'RED'
Color.RED.value           # 1
Color(2)                  # Color.GREEN   lookup by value
Color["BLUE"]             # Color.BLUE    lookup by name
Color.RED is Color.RED    # True          compare members with is / ==
Color.RED == 1            # False         a plain Enum is not equal to its value
list(Color)               # iterates in definition order
Color(9)                  # ValueError: 9 is not a valid Color
```

```python
class Status(StrEnum):            # 3.11+: members ARE strings (good for JSON/APIs)
    ACTIVE = auto()               # auto() gives the lowercase name: 'active'
    DISABLED = auto()

class Priority(IntEnum):          # members behave as ints
    LOW = 1
    HIGH = 3
Priority.HIGH > Priority.LOW      # True

class Perm(Flag):                 # bit flags, combine with | and &
    READ = auto()                 # 1
    WRITE = auto()                # 2
    EXEC = auto()                 # 4
rw = Perm.READ | Perm.WRITE
Perm.READ in rw                   # True

@unique                           # raises an error if two names share a value
class Level(Enum):
    DEBUG = 10
    INFO = 20
```

Members are immutable and hashable, so they work as dict keys and in `match`:

```python
match command:
    case Color.RED:
        print("stop")
```

## Enums in Go

Go has no `enum` keyword. The idiom is a named type plus constants generated with `iota`.

```go
type Weekday int

const (
    Sunday Weekday = iota    // 0
    Monday                   // 1
    Tuesday                  // 2
)

func (d Weekday) String() string {      // fmt uses this when printing
    switch d {
    case Sunday:
        return "Sunday"
    case Monday:
        return "Monday"
    case Tuesday:
        return "Tuesday"
    }
    return fmt.Sprintf("Weekday(%d)", int(d))
}

fmt.Println(Monday)        // Monday
fmt.Println(Weekday(9))    // Weekday(9): Go does NOT reject invalid values
```

```go
const (
    _  = iota                // skip 0
    KB = 1 << (10 * iota)    // 1024
    MB                       // 1048576
)

const (                      // bit flags
    Read = 1 << iota         // 1
    Write                    // 2
    Exec                     // 4
)
perm := Read | Write
perm&Write != 0              // true
```

> Start at `iota + 1` (or skip 0 with `_`) when you want the zero value to mean "unset".

---

# Equality, Identity and Copying

## == vs is in Python

`==` asks "do these have equal values?" (it calls `__eq__`). `is` asks "are these the very same object in memory?" (compare `id()`).

```python
a = [1, 2, 3]
b = [1, 2, 3]
c = a

a == b          # True   same contents
a is b          # False  two different list objects
a is c          # True   c is just another name for the same object
id(a) == id(c)  # True

x = None
x is None       # the correct way to test for None (it is a singleton)
```

Use `is` only for `None`, `True`, `False`, and sentinels; use `==` for everything else. Small integers and some strings are cached by CPython, so `is` can appear to work on them, but that is an implementation detail you must never rely on.

```python
a = 256; b = 256
a is b          # True in CPython (small ints are cached)
a = 1000; b = 1000
a is b          # may be False: depends on how the code was compiled

float("nan") == float("nan")   # False
```

You can define equality for your own classes:

```python
class Point:
    def __init__(self, x, y):
        self.x, self.y = x, y
    def __eq__(self, other):
        return isinstance(other, Point) and (self.x, self.y) == (other.x, other.y)
    def __hash__(self):                       # needed if equal objects must work as dict keys / set items
        return hash((self.x, self.y))
```

## Aliasing and Copying

Assignment never copies an object in Python; it adds another name for it. With mutable objects that is a common source of bugs.

```python
import copy

a = [[1, 2], [3, 4]]
b = a                       # same object
b.append([5, 6])            # a changes too

shallow = a.copy()          # also list(a) or a[:]: new outer list, SAME inner lists
deep = copy.deepcopy(a)     # everything copied recursively

a[0].append(99)
shallow[0]                  # [1, 2, 99]   the inner list is shared
deep[0]                     # [1, 2]       independent
```

## Equality in Go

`==` compares values for comparable types. Structs and arrays compare field by field. Pointers compare addresses, which is identity. Slices, maps, and functions can only be compared to `nil`.

```go
type P struct{ X, Y int }

p1, p2 := P{1, 2}, P{1, 2}
fmt.Println(p1 == p2)           // true    value equality (field by field)

pp1, pp2 := &p1, &p2
fmt.Println(pp1 == pp2)         // false   different addresses (identity)
fmt.Println(*pp1 == *pp2)       // true    compare the pointed-to values

a, b := []int{1, 2}, []int{1, 2}
// fmt.Println(a == b)          // compile error: slice can only be compared to nil
fmt.Println(slices.Equal(a, b)) // true
fmt.Println(maps.Equal(m1, m2)) // maps package
fmt.Println(reflect.DeepEqual(a, b)) // deep comparison of anything (slower, use in tests)
fmt.Println(bytes.Equal(x, y))  // for []byte

var e1, e2 error = io.EOF, io.EOF
e1 == e2                        // true: interfaces are equal if dynamic types and values are equal
```

Go assignment copies values: struct and array assignment is a full copy, while slices, maps, and channels copy only their small header, so the data underneath is shared (a shallow copy). A deep copy of nested data is written by hand or with a library.

---

# Memory, Lifetime and Garbage Collection

## Variables Are Names, Not Boxes (Python)

In Python a variable is a name bound to an object. Assignment rebinds the name; it does not copy data. Arguments are passed the same way ("pass by object reference"): the function gets another name for the same object.

```python
def mutate(lst):
    lst.append(1)         # changes the caller's list (same object)

def rebind(lst):
    lst = [9]             # only rebinds the local name; caller is unaffected

data = []
mutate(data)              # data == [1]
rebind(data)              # data == [1]
```

Immutable objects (`int`, `str`, `tuple`) cannot be changed in place, so "modifying" them always creates a new object.

## Stack and Heap

* The stack holds the data of function calls (local variables, return addresses). It is fast and cleaned up automatically when the function returns.
* The heap holds data that must outlive a single call or whose size is not known up front. It needs a strategy to free memory: manual (C), garbage collected (Python, Go, Java), or ownership rules checked at compile time (Rust).

In CPython every object lives on the heap; local variables are references to it. In Go the compiler decides: values that cannot outlive the function stay on the stack, values whose address escapes (returned pointers, captured by closures, stored in interfaces) are moved to the heap. This is called escape analysis, and you can see its decisions with `go build -gcflags=-m`. You never choose between stack and heap in Go code; you only choose whether to use a pointer.

## Object Lifetime and Reference Counting (Python)

CPython keeps a reference count on every object. When the count drops to zero the object is freed immediately. `del name` removes a name (one reference), not the object itself.

```python
import sys

a = []
sys.getrefcount(a)     # 2   (the name a, plus the temporary argument)
b = a
sys.getrefcount(a)     # 3
del b
sys.getrefcount(a)     # 2   (when it reaches 0 the list is freed)
```

Reference counting cannot free objects that reference each other (a cycle). Python adds a cyclic garbage collector that finds such unreachable groups periodically.

```python
import gc

class Node:
    def __init__(self):
        self.other = None

n1, n2 = Node(), Node()
n1.other, n2.other = n2, n1     # cycle: counts never reach zero by themselves
del n1, n2                       # now unreachable; the cyclic GC frees them later
gc.collect()                     # force a collection (rarely needed)
```

`__del__` (the finalizer) runs at an unpredictable time and is not a place to release important resources; use `with` blocks instead. `weakref` lets you refer to an object without keeping it alive (caches, observers).

## Garbage Collection

A garbage collector frees memory that the program can no longer reach. An object is reachable if some chain of references starts from a "root" (global variables, local variables on active call stacks) and leads to it.

* Python: reference counting plus a generational cyclic collector. Most objects are freed the instant their last reference disappears.
* Go: a concurrent, tri-color mark-and-sweep collector that runs alongside your goroutines with short pauses. You tune it with `GOGC` (how much the heap may grow before a cycle) and `GOMEMLIMIT` (a soft memory limit); `runtime.GC()` forces a cycle and `runtime.ReadMemStats` reports usage.
* Go and Python both promise you cannot use freed memory (no use-after-free) and do not need `free()`.

For contrast, manual memory management in C:

```c
// C (shown only to explain what GC removes)
int *p = malloc(sizeof(int) * 100);   // allocate 100 ints on the heap
p[0] = 42;
free(p);                               // forgetting this call = memory leak
// p[0] = 1;                           // use after free = undefined behavior, a classic security bug
```

## Memory Leaks

A garbage collector only frees what is unreachable. If your program keeps a reference to data it no longer needs, that data stays forever: that is a leak in a garbage-collected language.

Common causes:

* Global lists, dicts, or caches that only grow
* Listeners, callbacks, or timers that are registered and never removed
* Closures that capture large objects
* Go only: goroutines blocked forever on a channel, `time.Ticker` never stopped, a small sub-slice that keeps a huge backing array alive
* Not memory but just as bad: unclosed files, sockets, and database connections

```python
cache = {}                                  # grows forever

def get_user(user_id):
    if user_id not in cache:
        cache[user_id] = load_from_db(user_id)
    return cache[user_id]

# Fix: bound the cache
from functools import lru_cache

@lru_cache(maxsize=1024)
def get_user(user_id):
    return load_from_db(user_id)
```

```go
func leak() {
    ch := make(chan int)
    go func() {
        v := <-ch                            // nobody ever sends: this goroutine lives forever
        fmt.Println(v)
    }()
}

big := make([]byte, 100<<20)                 // 100 MB
small := big[:10]                            // keeps all 100 MB alive
small = append([]byte(nil), big[:10]...)     // copy what you need so big can be freed
```

Finding leaks: Python has `tracemalloc` and `objgraph`; Go has `runtime.NumGoroutine()` and pprof (`net/http/pprof`, then `go tool pprof`). If memory or goroutine count climbs steadily under constant load, something is leaking.

## Pointers and Values in Go

Python hides memory addresses behind references. Go has real pointers, but with no pointer arithmetic, and the garbage collector keeps everything a pointer refers to alive.

```go
x := 10
p := &x            // & = address of: p has type *int
fmt.Println(*p)    // 10   * = dereference: read the value p points to
*p = 20            // writes through the pointer: x is now 20
fmt.Println(x)     // 20

q := new(int)      // *int pointing to a new zero int
func newInt() *int {
    v := 5
    return &v      // safe: v escapes to the heap and lives as long as the pointer does
}
```

Go always passes by value: a function receives a copy of every argument. Passing a pointer copies the pointer (a small address), and both copies point at the same data.

```go
func incr(n int)      { n++ }        // changes a copy
func incrPtr(n *int)  { *n++ }       // changes the original

v := 1
incr(v)         // v == 1
incrPtr(&v)     // v == 2
```

* Large structs are cheaper to pass by pointer, and a function can only modify the caller's struct through a pointer
* Slices, maps, and channels already contain internal pointers, so changes to their elements are visible to the caller even though the header itself was copied
* A `nil` pointer is the zero value; check before dereferencing

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

## Built-in Functions in Go

Go keeps its built-ins few; the rest lives in the standard library packages (`strings`, `slices`, `maps`, `sort`, `math`, `strconv`).

```go
len(x)                      // length of string, array, slice, map, channel
cap(x)                      // capacity of slice, array, channel
append(s, 1, 2)             // add to a slice (returns the new slice)
copy(dst, src)              // copy slice elements, returns how many were copied
delete(m, key)              // remove a map key
make([]int, 0, 10)          // create slice, map, or channel
make(map[string]int)
make(chan int, 3)
new(int)                    // pointer to a new zero value
min(3, 1, 2)                // 1   (Go 1.21+)
max(3, 1, 2)                // 3
clear(m)                    // empties a map, zeroes slice elements (Go 1.21+)
close(ch)                   // close a channel
panic("boom")               // stop normal execution (see Exception Handling)
recover()                   // regain control inside a deferred function
```

```go
// Python's sorted / enumerate / any / zip equivalents
nums := []int{3, 1, 2}
slices.Sort(nums)                        // sorts in place: [1 2 3]
sort.Slice(people, func(i, j int) bool { // custom ordering
    return people[i].Age < people[j].Age
})
slices.Contains(nums, 2)                 // true
slices.Index(nums, 3)                    // 2
slices.Max(nums)                         // 3
slices.Reverse(nums)
for i, v := range nums {}                // enumerate is built into range
// map/filter: write a loop, or a small generic helper (see Generics)
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

## Return Values, Keyword-Only Arguments and Docstrings

```python
def min_max(nums):
    return min(nums), max(nums)        # really returns ONE tuple
low, high = min_max([3, 1, 2])         # unpacked into two names

def connect(host, port, *, timeout=5): # arguments after * must be passed by keyword
    ...
connect("db", 5432, timeout=10)

def area(r, /):                        # arguments before / must be positional
    """Return the area of a circle.    <- docstring: shown by help(area) and IDEs"""
    return 3.14159 * r ** 2

def no_return():
    pass
no_return()                            # None: no return statement means None
```

## Common Function Pitfalls

```python
# Mutable default argument: the default is created ONCE, when def runs
def add_item(item, items=[]):
    items.append(item)
    return items

add_item(1)        # [1]
add_item(2)        # [1, 2]   surprise: the same list is reused

def add_item(item, items=None):        # fix: use None and create inside
    if items is None:
        items = []
    items.append(item)
    return items
```

## Functions in Go

```go
func add(a, b int) int {                 // parameter types after names; result type after the parens
    return a + b
}

func divmod(a, b int) (int, int) {       // multiple return values are built in
    return a / b, a % b
}
q, r := divmod(9, 2)                     // 4 1

func split(sum int) (x, y int) {         // named results start as zero values
    x = sum * 4 / 9
    y = sum - x
    return                               // "naked" return: use sparingly, only in short functions
}

func total(nums ...int) int {            // variadic: nums is a []int inside
    sum := 0
    for _, n := range nums {
        sum += n
    }
    return sum
}
total(1, 2, 3)
total([]int{1, 2, 3}...)                 // spread a slice with ...

double := func(x int) int { return x * 2 }   // function value / anonymous function
func apply(f func(int) int, v int) int { return f(v) }   // functions are first-class
```

* Go has no default arguments, no keyword arguments, and no function overloading. When a function needs many settings, pass an options struct or use the "functional options" pattern.
* Return an `error` as the last result for anything that can fail (see Exception Handling).
* Functions can be stored in variables, passed, returned, and used as closures (see Scope and Lifetime).

---

# Scope and Lifetime

Scope answers "where in the source code can this name be used?" It is decided by the text of the program. Lifetime answers "how long does the value exist while the program runs?" It is decided at run time. They are different things.

## Local and Global Variables in Python

A name assigned inside a function is local to that function. A name assigned at the top of a module is global to that module. Reading a global from inside a function works; assigning to it creates a new local unless you declare `global`.

```python
counter = 0                      # module-level (global)

def read():
    print(counter)               # reads the global: fine

def broken():
    counter += 1                 # UnboundLocalError: assignment makes counter local, but it has no value yet

def works():
    global counter               # opt in to rebinding the module-level name
    counter += 1
```

Prefer passing values in and returning results over `global`; shared mutable globals make code hard to test and unsafe with threads.

## Lexical Scope and the LEGB Rule

Python resolves a name by looking in these places, in order: Local, Enclosing function(s), Global (module), Built-in. Lexical (static) scope means the lookup follows where the code is written, not where it is called from.

```python
x = "global"

def outer():
    x = "enclosing"
    def inner():
        print(x)                 # found in the enclosing function
    return inner

def caller():
    x = "caller"                 # irrelevant: inner was not written inside caller
    outer()()

caller()                         # prints "enclosing"
```

```python
def counter():
    count = 0
    def inc():
        nonlocal count           # rebind the variable in the enclosing function
        count += 1
        return count
    return inc
```

## Block Scope

Python has no block scope: `if`, `for`, and `while` bodies share the enclosing function's scope, so loop variables stay alive after the loop. Functions, classes, modules, and comprehensions create new scopes.

```python
for i in range(3):
    last = i
print(i, last)                   # 2 2   both still exist

squares = [n * n for n in range(3)]
# print(n)                       # NameError: comprehension variables do not leak
```

Go has block scope: every `{ }` creates a scope, and a variable declared inside it disappears at the closing brace. Names declared outside any function belong to the package, and imports are scoped to the file.

```go
func main() {
    x := 1
    if true {
        y := 2                   // y exists only inside this block
        fmt.Println(x, y)
    }
    // fmt.Println(y)            // compile error: undefined: y
}
```

## Shadowing

Shadowing means an inner name hides an outer name that has the same spelling.

```python
len = 5                          # shadows the built-in len for the whole module
# len([1, 2])                    # TypeError: 'int' object is not callable
list = [1, 2]                    # a very common beginner bug: now list(...) no longer works
```

In Go the classic trap is `:=` inside a block, which creates a new variable instead of assigning to the outer one:

```go
var err error
if cond {
    x, err := doWork()           // NEW x and NEW err: shadow the outer err
    _ = x
}
fmt.Println(err)                 // still nil, the error was lost

// Fix: declare the other variables first and use = instead of :=
var x int
if cond {
    x, err = doWork()
}
```

## Closures and Late Binding

A closure is a function that remembers variables from the scope where it was created, even after that scope has finished. It captures the variables themselves, not copies of their values at that moment.

```python
funcs = [lambda: i for i in range(3)]
[f() for f in funcs]             # [2, 2, 2]   every lambda sees the same i, whose final value is 2

funcs = [lambda i=i: i for i in range(3)]   # bind the current value as a default argument
[f() for f in funcs]             # [0, 1, 2]
```

```go
func counter() func() int {
    count := 0                   // captured by the closure
    return func() int {
        count++
        return count
    }
}

next := counter()
next()                           // 1
next()                           // 2

// Since Go 1.22 each loop iteration has its own variable, so this prints 0 1 2 (in some order)
for i := 0; i < 3; i++ {
    go func() { fmt.Println(i) }()
}
// Before Go 1.22 all goroutines shared one i and usually printed 3 3 3
```

## Module and Package Namespaces

A namespace is a mapping from names to objects. Each Python module has its own global namespace, so two modules can both define `connect` without clashing. Importing adds a name to your namespace.

```python
import math                      # binds the name "math" in this module
from math import sqrt            # binds "sqrt" directly (a separate reference to the same function)
import math as m                 # binds the alias "m"

math.pi                          # attribute lookup in the math module's namespace
dir(math)                        # names inside it
globals()                        # this module's namespace as a dict
```

`from module import name` makes a new name that points to the object at import time; if the module later rebinds its own variable, your name does not follow. In Go, each package is a namespace and you write `pkg.Name`; only names that begin with an uppercase letter are exported (visible to other packages), lowercase names are private to the package.

## Scope vs Lifetime

* Scope = where a name is visible (a property of the source text)
* Lifetime = how long the value exists (a property of the running program)

They often disagree:

```python
def make_counter():
    count = 0                    # scope: make_counter's body and the inner function
    def inc():
        nonlocal count
        count += 1
        return count
    return inc

c = make_counter()               # make_counter has returned, its scope is gone
c(); c()                         # but the value behind count is still alive (2), because inc references it
```

```go
func newInt() *int {
    v := 42                      // scope: this function only
    return &v                    // lifetime: as long as anyone holds the pointer (moved to the heap)
}
p := newInt()
fmt.Println(*p)                  // 42
```

The opposite also happens: a name can still be in scope while the thing it refers to is finished with, such as a closed file object or a closed network connection. Scope never tells you whether a resource is still usable; cleanup rules (`with`, `defer`) do.

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

## What Happens When You import

Importing is not just a lookup; it runs code. The first time a module is imported in a program, Python does this, in order:

* Checks `sys.modules`, a cache of already-imported modules. If the module is there, it is reused and nothing else happens.
* Searches for the module along `sys.path` (next section).
* Compiles the file to bytecode (cached in `__pycache__`) and executes all its top-level code once.
* Stores the resulting module object in `sys.modules` and binds a name to it in the importing file.

```python
# file: tools.py
print("tools is being loaded")        # top-level code runs at import time
VALUE = 42

# file: main.py
import tools                           # prints: tools is being loaded
import tools                           # prints nothing: served from sys.modules
```

This is why module top level should only define things (functions, classes, constants) and not start work such as network calls or file writes.

## Module Search Path

`sys.path` is a list of folders Python searches, in this order:

* The folder of the script being run (or the current directory for `-c`, `-m`, and the REPL)
* Folders listed in the `PYTHONPATH` environment variable
* The standard library folders
* `site-packages` of the active Python, which is where `pip install` puts packages (inside the venv when one is active)

```bash
python -c "import sys; print(sys.path)"
python -c "import requests; print(requests.__file__)"    # which file did that import load?
python -m pip install requests                             # installs into the SAME Python that "python" runs
```

Typical problems and what they mean:

* `ModuleNotFoundError: No module named 'x'` — the package is not installed in the interpreter that is running (wrong or inactive venv), or you are in a different folder than you think
* Naming your own file `random.py`, `json.py`, or `email.py` hides the standard module of the same name, because the script's folder is searched first
* `pip` and `python` pointing at different installations; always use `python -m pip`

## Packages in Depth

A module is a single `.py` file. A package is a folder of modules; `__init__.py` marks it as a package and runs when the package is imported (it can be empty). Folders without `__init__.py` still work as "namespace packages", but add the file for regular projects.

```python
# mypackage/__init__.py
__all__ = ["func_a"]             # what "from mypackage import *" exports
```

Relative imports (`from . import x`) only work when the file is part of a package that was imported, so run package code as a module:

```bash
python mypackage/module_a.py     # ImportError: attempted relative import with no known parent package
python -m mypackage.module_a     # works: Python knows module_a belongs to mypackage
```

## What `if __name__ == "__main__":` Means

Every module has a built-in variable `__name__`. When Python runs a file directly, it sets `__name__` to the string `"__main__"`. When the same file is imported by another module, `__name__` is the module's own name. The `if` check therefore means "run this block only when the file is executed as the program, not when it is imported".

```python
# file: tools.py
print("tools __name__ is", __name__)

def helper():
    return "helping"

if __name__ == "__main__":
    print("running tools directly")     # skipped on import
    print(helper())
```

```bash
python tools.py
# tools __name__ is __main__
# running tools directly
# helping

python -c "import tools"
# tools __name__ is tools
```

Why it matters:

* A file can be both a reusable library (import it) and a runnable program (execute it) without side effects at import
* Test runners and other modules can import your functions safely
* `multiprocessing` on Windows and macOS re-imports your main file in child processes, so work started at top level without this guard would run again and again
* A package can have a `__main__.py`, which is what `python -m mypackage` runs

Go has no such check. The entry point is explicit: the package named `main` containing `func main()` builds an executable, and every other package is a library. A package's `init()` function runs automatically when it is imported, so keep `init()` tiny.

## Circular Imports

If `a.py` imports `b.py` and `b.py` imports `a.py`, one of them is used before it has finished loading, and you get errors like `ImportError: cannot import name ... (most likely due to a circular import)`.

```python
# Ways out
# - Move the shared code into a third module that both import
# - Import inside the function that needs it (delays the import until call time)
# - Depend on a small interface/base class instead of the concrete module

def process():
    from b import helper         # imported only when process() runs
    return helper()
```

Go refuses circular package imports outright (`import cycle not allowed`), which forces layered design from the beginning.

## Packages and Modules in Go

A package is a folder of `.go` files that all start with the same `package name` line. A module is a tree of packages with a `go.mod` file at its root. An import path is the module path plus the folder.

```text
myapp/
├── go.mod                      # module github.com/alice/myapp
├── main.go                     # package main
├── store/
│   └── store.go                # package store   -> import "github.com/alice/myapp/store"
└── internal/
    └── auth/
        └── auth.go             # package auth    -> only importable from inside myapp
```

```go
// store/store.go
package store

type Store struct{ items map[string]string }   // uppercase = exported (visible to other packages)

func New() *Store { return &Store{items: map[string]string{}} }
func (s *Store) Put(k, v string) { s.items[k] = v }
func helper() {}                                // lowercase = private to the package

// main.go
package main

import (
    "fmt"
    "github.com/alice/myapp/store"      // module path + folder
)

func main() {
    s := store.New()
    s.Put("a", "1")
    fmt.Println("ok")
}
```

* Exported means the identifier starts with a capital letter; there is no `public` or `private` keyword
* The folder name is conventionally the package name; one folder holds exactly one package (plus its `_test` package)
* A directory named `internal` can only be imported by code inside its parent module path
* An import you do not use is a compile error; `import _ "pkg"` imports only for side effects (such as registering a database driver)

---

# Dependencies and Environments

## Why Environments Exist

If every Python project installs libraries into one global place, projects collide: project A needs `requests 2.25`, project B needs `requests 2.31`, and only one version can be installed at a time. A virtual environment is a private folder with its own Python executable links and its own `site-packages`, so each project gets exactly the versions it needs.

## venv

```bash
python -m venv .venv                 # create (once per project)

source .venv/bin/activate            # Linux/macOS
.venv\Scripts\activate               # Windows cmd
.venv\Scripts\Activate.ps1           # Windows PowerShell

which python                         # now points inside .venv (where python on Windows)
python -m pip install requests       # installs into .venv only
deactivate                           # leave the environment
rm -rf .venv                         # delete it; you can always recreate it from your dependency file
```

Rules that save pain: one environment per project, never commit `.venv/` (put it in `.gitignore`), never use `sudo pip install`, and if Linux says `externally-managed-environment`, that is the system protecting its own Python, so use a venv.

## pip

```bash
python -m pip install requests                  # latest version
python -m pip install requests==2.31.0          # exact version
python -m pip install "requests>=2.31,<3"       # a range (quotes stop the shell treating > as redirection)
python -m pip install --upgrade requests
python -m pip uninstall requests
python -m pip list                              # everything installed
python -m pip list --outdated
python -m pip show requests                     # version, location, what it requires
python -m pip freeze > requirements.txt         # exact versions of everything installed
python -m pip install -r requirements.txt       # recreate an environment
python -m pip check                             # report broken or conflicting dependencies
python -m pip install -e .                      # editable install of your own project
```

## Dependency Versions

Most libraries use semantic versioning: MAJOR.MINOR.PATCH (for example 2.31.0). A patch release fixes bugs, a minor release adds features compatibly, and a major release may break your code.

```text
requests==2.31.0      exactly this version (a "pin")
requests>=2.31        this or newer
requests>=2.31,<3     newer than 2.31 but below the next major release
requests~=2.31        compatible release: >=2.31 and <3
requests~=2.31.0      >=2.31.0 and <2.32
requests!=2.30.0      anything except a known-bad version
```

Your own project's dependencies are called direct; the libraries those pull in are transitive. A fresh `pip install` of the same requirement list can give different transitive versions next month, which is why applications need pinned, reproducible versions.

```toml
# pyproject.toml: the modern, standard place for project metadata and dependencies
[project]
name = "myapp"
version = "0.1.0"
requires-python = ">=3.10"
dependencies = [
    "requests>=2.31,<3",
    "flask~=3.0",
]

[project.optional-dependencies]
dev = ["pytest", "ruff", "mypy"]
```

* Applications: keep loose requirements in `pyproject.toml` (or `requirements.in`) and generate a fully pinned lock (`requirements.txt` from `pip freeze` or `pip-compile`; `uv` and Poetry write lock files for you) that you install in production
* Libraries: declare wide, honest ranges so users can combine your library with others

## Dependency Conflicts and How to Avoid Them

A conflict happens when two requirements cannot both be satisfied. Python allows only one version of a package per environment, so if library X needs `urllib3<2` and library Y needs `urllib3>=2`, pip stops with `ResolutionImpossible`.

* Use a separate virtual environment for every project (and a separate container image for every deployed service)
* Pin and lock versions for applications, and commit the lock file
* Upgrade on purpose, in small steps, with tests running (`pip list --outdated`, then bump one thing at a time)
* Run `pip check` in CI to catch broken combinations early
* Keep the dependency list small: every extra library is another source of conflicts and security problems
* Inspect the tree when something fights: `python -m pip show pkg`, `pipdeptree`; scan for known vulnerabilities with `pip-audit`

## Dependencies in Go (Go Modules)

Every Go project is a module. The file `go.mod` records the module's name, the Go version, and the minimum version of each dependency. The file `go.sum` stores cryptographic hashes of the exact contents of each dependency, so a downloaded module that has been tampered with is rejected.

```text
module github.com/alice/myapp

go 1.23

require (
    github.com/google/uuid v1.6.0
    github.com/gorilla/mux v1.8.1
    golang.org/x/text v0.14.0 // indirect
)
```

How Go modules manage dependencies:

* Each dependency is listed with an exact version; `// indirect` marks ones your code does not import directly but that are needed
* Minimal version selection: if your project needs library C at v1.2 through one dependency and v1.4 through another, Go picks v1.4, the highest of the minimum versions requested, not the newest release. Builds do not change just because a new version was published; you upgrade on purpose
* Downloads come through a module proxy (`proxy.golang.org`) and are checked against a public checksum database; they are cached in `$GOPATH/pkg/mod`
* A module with major version 2 or higher uses `/v2` in its import path, so v1 and v2 can coexist in one build
* The lock-file job is done by `go.mod` plus `go.sum`, so there is no separate lock step

```bash
go mod init github.com/alice/myapp     # create go.mod
go get github.com/google/uuid          # add a dependency (latest) and update go.mod/go.sum
go get github.com/google/uuid@v1.6.0   # a specific version
go get -u ./...                        # upgrade dependencies of the packages in this module
go mod tidy                            # add missing, remove unused requirements (run before committing)
go mod download                        # fetch everything into the module cache
go mod verify                          # check cached modules against go.sum
go mod why github.com/google/uuid      # why is this dependency here?
go list -m all                         # the full list of modules and versions in the build
go mod vendor                          # copy dependencies into ./vendor for offline builds
```

For local development you can point a dependency at a folder with a `replace` directive in `go.mod`, or work on several modules together with a `go.work` file.

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

## API Calls in Go

The standard library has everything needed (`net/http` and `encoding/json`), with no third-party package.

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "net/http"
    "time"
)

type User struct {
    Login string `json:"login"`      // struct tags map JSON keys to fields
    ID    int    `json:"id"`
}

// A client with a timeout: the default http.Client has none, and a hung request would wait forever
var client = &http.Client{Timeout: 5 * time.Second}

func getUser(name string) (*User, error) {
    resp, err := client.Get("https://api.github.com/users/" + name)
    if err != nil {
        return nil, fmt.Errorf("request failed: %w", err)
    }
    defer resp.Body.Close()                       // always close the body

    if resp.StatusCode != http.StatusOK {         // Go does not raise for 4xx/5xx: check it yourself
        return nil, fmt.Errorf("unexpected status: %s", resp.Status)
    }

    var u User
    if err := json.NewDecoder(resp.Body).Decode(&u); err != nil {
        return nil, fmt.Errorf("decode: %w", err)
    }
    return &u, nil
}

func createPost() error {
    payload, _ := json.Marshal(map[string]any{"title": "Hello", "body": "World", "userId": 1})
    resp, err := client.Post("https://jsonplaceholder.typicode.com/posts", "application/json", bytes.NewReader(payload))
    if err != nil {
        return err
    }
    defer resp.Body.Close()
    fmt.Println(resp.StatusCode)                  // 201
    return nil
}
```

```go
// A tiny server (the Go version of the Flask example)
func main() {
    mux := http.NewServeMux()
    mux.HandleFunc("GET /hello", func(w http.ResponseWriter, r *http.Request) {
        w.Header().Set("Content-Type", "application/json")
        json.NewEncoder(w).Encode(map[string]string{"message": "Hello, World!"})
    })
    mux.HandleFunc("POST /add", func(w http.ResponseWriter, r *http.Request) {
        var in struct{ A, B int }
        if err := json.NewDecoder(r.Body).Decode(&in); err != nil {
            http.Error(w, "bad json", http.StatusBadRequest)
            return
        }
        json.NewEncoder(w).Encode(map[string]int{"result": in.A + in.B})
    })
    http.ListenAndServe(":8080", mux)             // blocks; each request runs in its own goroutine
}
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

## Exceptions and Error Propagation

An exception is an object that represents a failure. When code raises one, normal execution stops, and Python unwinds the call stack, leaving each function in turn, until some `except` block handles it. If none does, the program prints a traceback and exits with a non-zero status.

```python
def parse_age(text):
    return int(text)               # raises ValueError for "abc"

def load_user(text):
    return {"age": parse_age(text)}   # no try here: the error passes straight through

def main():
    try:
        user = load_user("abc")
    except ValueError:
        print("Please enter a number")   # handled here, at the level that can do something useful
```

Handle an error where you can do something meaningful about it (ask again, use a default, report to the user). In between, let it propagate.

## try, except, else, finally, raise

```python
def read_number(path):
    try:
        with open(path) as f:                 # code that may fail goes in try
            value = int(f.read())
    except FileNotFoundError:                 # runs only if that exception happened
        print("file missing")
        return None
    except (ValueError, TypeError) as e:      # one handler for several types
        print(f"bad content: {e}")
        raise                                  # bare raise re-raises the same exception
    else:
        print("read OK")                       # runs only if try finished WITHOUT an exception
        return value
    finally:
        print("done")                          # ALWAYS runs: success, handled error, return, or crash
```

* `try` — the code that might fail; keep it as short as possible
* `except` — handles a specific exception type; the first matching handler wins, so put specific types before general ones
* `else` — code that should run only on success but should not be protected by the `except` (so it cannot accidentally hide its own errors)
* `finally` — cleanup that must happen no matter what (closing, unlocking, rolling back)
* `raise` — throw an exception: `raise ValueError("msg")`, or `raise` alone inside `except` to re-throw
* `raise NewError("...") from e` — raise a different exception while keeping the original as the cause; `from None` hides the cause

```python
# Exception chaining: translate low-level errors into your application's language
try:
    config = json.loads(text)
except json.JSONDecodeError as e:
    raise ConfigError("invalid config file") from e
```

A `return` inside `finally` overrides any exception or return value from the `try`, which is almost never what you want; do not put `return` in `finally`.

## Exception Hierarchy

```text
BaseException
├── SystemExit, KeyboardInterrupt      # do NOT catch these by accident
└── Exception                          # base for normal errors
    ├── ValueError                     # right type, wrong value:   int("abc")
    ├── TypeError                      # wrong type:                 len(5)
    ├── KeyError, IndexError           # missing key / index         (both are LookupError)
    ├── AttributeError                 # missing attribute:          None.name
    ├── ZeroDivisionError
    ├── OSError                        # FileNotFoundError, PermissionError, TimeoutError, ConnectionError
    ├── ImportError                    # ModuleNotFoundError
    ├── StopIteration
    └── RuntimeError
```

`except:` (bare) and `except BaseException:` catch `Ctrl+C` and `sys.exit()` too. Catch the narrowest type you can, and `Exception` only at the outermost layer of a program.

## Validation

Validate data where it enters your program (user input, files, network, environment variables) and fail early with a clear message. Inside, trust the data you already checked.

```python
def set_age(age):
    if not isinstance(age, int):
        raise TypeError("age must be an int")
    if not 0 <= age <= 150:
        raise ValueError(f"age out of range: {age}")
    return age

# Two styles for handling possible failure
# LBYL: look before you leap
if "key" in data:
    value = data["key"]

# EAFP: easier to ask forgiveness than permission (common in Python, avoids race conditions)
try:
    value = data["key"]
except KeyError:
    value = None
```

`assert` is for internal assumptions that must always be true (`assert len(items) > 0`); it is removed when Python runs with `-O`, so never use it to validate user input.

## Cleanup After Failure

Resources (files, locks, sockets, database connections, temporary files) must be released even when something fails halfway. In Python use a `with` statement; it calls the object's cleanup code on the way out, exactly like `finally`.

```python
with open("data.txt") as f:        # file is closed even if an exception happens inside
    process(f)

with lock:                         # lock is released even on error
    update_shared_state()

# Write your own context manager
from contextlib import contextmanager

@contextmanager
def transaction(conn):
    try:
        yield conn
        conn.commit()              # success path
    except Exception:
        conn.rollback()            # failure path: undo the partial work
        raise                      # and still report the error
    finally:
        conn.close()

with transaction(connect()) as conn:
    conn.execute("...")
```

Use `contextlib.suppress(FileNotFoundError)` when ignoring one specific, expected error is genuinely correct.

## Never Silently Swallow Errors

An error that is caught and thrown away turns into a mystery later: wrong data, missing records, a program that "works" but is wrong.

```python
# Bad: hides every problem, even typos and Ctrl+C
try:
    save(order)
except:
    pass

# Bad: catches too much and returns a value that looks like success
def get_price(item):
    try:
        return prices[item]
    except Exception:
        return 0

# Good: specific, logged with the traceback, and the caller still learns about the failure
import logging
log = logging.getLogger(__name__)

try:
    save(order)
except DatabaseError:
    log.exception("could not save order %s", order.id)   # logs message + full traceback
    raise
```

* Catch specific exceptions
* Either handle an error or let it propagate; do not log it and also pretend it did not happen
* Add context when re-raising (`raise ... from e`), never delete it
* Log once, at the layer that finally handles the error, to avoid duplicate noise

## Custom Exceptions

```python
class AppError(Exception):
    """Base class for all errors raised on purpose by this application."""

class NotFoundError(AppError):
    def __init__(self, resource, resource_id):
        super().__init__(f"{resource} {resource_id} not found")
        self.resource = resource
        self.resource_id = resource_id

class ValidationError(AppError):
    pass

try:
    raise NotFoundError("user", 42)
except AppError as e:              # one handler for everything the app raises deliberately
    print(e)
```

## Recovering from a Failed Operation

First decide what kind of failure it is.

* Transient (timeout, connection reset, 503, lock contention): retrying often succeeds
* Permanent (invalid input, 404, permission denied, a bug): retrying never helps; report it
* Partial (step 3 of 5 failed): undo or compensate the finished steps, or make the operation safe to run again

Recovery options, from least to most drastic: retry with exponential backoff (a small, limited number of times, with jitter, and only for operations that are safe to repeat, meaning idempotent), fall back to a default or cached value, degrade a feature while keeping the rest running, roll back, or stop and tell the user or the operator clearly what failed. Always set timeouts on anything that crosses a network.

```python
import random
import time

def retry(func, attempts=3, base_delay=0.5, retry_on=(ConnectionError, TimeoutError)):
    for attempt in range(1, attempts + 1):
        try:
            return func()
        except retry_on:
            if attempt == attempts:
                raise                                           # out of attempts: let the caller decide
            delay = base_delay * 2 ** (attempt - 1)             # 0.5s, 1s, 2s ...
            time.sleep(delay + random.uniform(0, 0.1))          # jitter avoids many clients retrying in lockstep
```

The outermost layer of an application is where unexpected errors end up: log them, show a clean message, and exit with a non-zero code so schedulers and CI know it failed.

```python
import logging
import sys

def main():
    ...

if __name__ == "__main__":
    try:
        main()
    except KeyboardInterrupt:
        sys.exit(130)                                  # conventional exit code for Ctrl+C
    except AppError as e:
        logging.error("%s", e)                         # expected failure: short message, no traceback
        sys.exit(1)
    except Exception:
        logging.exception("unexpected failure")        # unexpected: full traceback in the log
        sys.exit(2)
```

## Errors as Values in Go

Go has no exceptions for ordinary failures. A function that can fail returns an `error` as its last result, and the caller checks it right away. `error` is just a built-in interface with one method, `Error() string`, and `nil` means success.

```go
func loadConfig(path string) (Config, error) {
    data, err := os.ReadFile(path)
    if err != nil {
        return Config{}, fmt.Errorf("load config %q: %w", path, err)   // add context, keep the cause
    }
    var cfg Config
    if err := json.Unmarshal(data, &cfg); err != nil {
        return Config{}, fmt.Errorf("parse config %q: %w", path, err)
    }
    return cfg, nil
}

cfg, err := loadConfig("app.json")
if err != nil {
    log.Fatal(err)                  // or return err to the caller, or handle it
}
```

Why Go code is full of `if err != nil`:

* Errors are ordinary return values, not hidden control flow: every place that can fail is visible in the code
* The compiler pushes you to deal with the error right where it happens, with the context that makes the best decision possible
* You can see the success path by reading straight down the left margin, and each failure path ends in an early `return`
* The cost is repetition, which is the accepted trade-off; the benefit is that nothing jumps out of a function unexpectedly

## Creating, Wrapping and Inspecting Errors in Go

```go
import (
    "errors"
    "fmt"
    "io/fs"
    "os"
)

var ErrNotFound = errors.New("not found")          // sentinel error: compare with errors.Is

type ValidationError struct {                      // custom error type: carries data
    Field string
    Msg   string
}
func (e *ValidationError) Error() string { return e.Field + ": " + e.Msg }

func validate(age int) error {
    if age < 0 {
        return &ValidationError{Field: "age", Msg: "must be >= 0"}
    }
    return nil
}

func find(id int) error {
    return fmt.Errorf("find user %d: %w", id, ErrNotFound)   // %w wraps, building a chain
}

err := find(7)
fmt.Println(err)                                   // find user 7: not found
errors.Is(err, ErrNotFound)                        // true: searches the whole chain
errors.Unwrap(err)                                 // the wrapped error: ErrNotFound

var ve *ValidationError
if errors.As(validate(-1), &ve) {                  // finds an error of a given type in the chain
    fmt.Println(ve.Field)                          // age
}

_, err = os.Open("missing.txt")
errors.Is(err, fs.ErrNotExist)                     // true: works through the library's wrapping

errors.Join(err1, err2)                            // combine several errors into one
```

Rules of thumb: wrap with `%w` when the caller may want to inspect the cause, add the operation name so the message reads like a trail (`load config: read file: permission denied`), and handle an error once, either return it or log it, not both.

## panic and recover

`panic` is for situations that should not happen: a programming bug or an impossible state. It stops the current function, runs its deferred calls, and unwinds up the goroutine's stack; if nothing recovers, the program crashes with a stack trace. Runtime errors (nil dereference, index out of range) are panics too.

```go
func mustPositive(n int) int {
    if n <= 0 {
        panic(fmt.Sprintf("n must be positive, got %d", n))
    }
    return n
}

func safe() (err error) {
    defer func() {
        if r := recover(); r != nil {            // recover only works inside a deferred function
            err = fmt.Errorf("recovered: %v", r)
        }
    }()
    mustPositive(-1)
    return nil
}
```

Do not use panic for ordinary errors such as bad input or a missing file. Use `recover` at boundaries (an HTTP server recovers per request so one bad request does not kill the process), and turn the panic into an error or a 500 response.

## try, except, raise, finally in Go Terms

| Python | Go |
| :--- | :--- |
| `try` / `except` | call the function, then `if err != nil { ... }` right after |
| `else` (success path) | the code after the `if err != nil` block, not indented |
| `raise Error(...)` | `return fmt.Errorf(...)` or `return &MyError{...}` |
| `raise ... from e` | `fmt.Errorf("...: %w", e)` |
| `except SomeError` | `errors.Is(err, X)` or `errors.As(err, &target)` |
| `finally` / `with` | `defer` |
| bug that should crash | `panic(...)`, optionally `recover()` at a boundary |
| custom exception class | any type with an `Error() string` method |

## Resource Cleanup with defer

`defer` schedules a function call to run when the surrounding function returns, whether it returns normally, through an early `return`, or because of a panic. It keeps the cleanup right next to the code that acquired the resource.

```go
func process(path string) error {
    f, err := os.Open(path)
    if err != nil {
        return err
    }
    defer f.Close()                  // runs when process returns, on every path

    // ... many lines, many possible early returns ...
    return nil
}

mu.Lock()
defer mu.Unlock()                    // the standard lock/unlock pairing
```

When does a deferred function run? At the moment the function that contains the `defer` returns (not at the end of the block, unlike Python's `with`). Details that matter:

```go
// Several defers run in last-in, first-out order
func demo() {
    defer fmt.Println("first deferred")     // runs last
    defer fmt.Println("second deferred")    // runs first
    fmt.Println("body")
}
// output: body / second deferred / first deferred

// Arguments are evaluated when the defer statement executes, not when it runs
x := 1
defer fmt.Println("x was", x)               // prints "x was 1"
x = 2

// A deferred closure sees later changes and can set named results
func readAll(path string) (err error) {
    f, err := os.Open(path)
    if err != nil {
        return err
    }
    defer func() {
        if cerr := f.Close(); err == nil {  // do not lose a Close error on a successful write
            err = cerr
        }
    }()
    return nil
}
```

Defer inside a loop is a trap: every deferred call waits until the whole function returns, so thousands of files would stay open.

```go
for _, name := range files {
    f, _ := os.Open(name)
    defer f.Close()                         // BAD: nothing closes until the function ends
}

for _, name := range files {
    if err := processFile(name); err != nil {   // GOOD: processFile has its own defer
        return err
    }
}
```

## Recovering from Failed Operations in Go

The same ideas apply: retry only transient errors, limit attempts, back off, respect cancellation, and wrap the final error.

```go
func retry(ctx context.Context, attempts int, delay time.Duration, fn func() error) error {
    var err error
    for i := 0; i < attempts; i++ {
        if err = fn(); err == nil {
            return nil
        }
        select {
        case <-time.After(delay << i):          // 1x, 2x, 4x ... exponential backoff
        case <-ctx.Done():                       // stop waiting if the caller gave up
            return ctx.Err()
        }
    }
    return fmt.Errorf("after %d attempts: %w", attempts, err)
}
```

At the top of the program, `main` decides the exit code: print the error and call `os.Exit(1)` there, and nowhere deeper (deferred calls do not run after `os.Exit`).

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

## Modes, Encoding and Paths in Python

```python
# Modes: "r" read (default), "w" write (erases the file!), "a" append, "x" create (fails if it exists)
# Add "b" for binary ("rb", "wb"), "+" for read and write ("r+")

with open("data.txt", "a", encoding="utf-8") as f:       # always state the encoding for text
    f.write("one more line\n")

with open("data.txt", encoding="utf-8") as f:
    for line in f:                                         # reads one line at a time: fine for huge files
        print(line.rstrip("\n"))

from pathlib import Path

p = Path("logs") / "app.log"                               # build paths with / (works on every OS)
p.parent.mkdir(parents=True, exist_ok=True)
p.write_text("hello", encoding="utf-8")
p.read_text(encoding="utf-8")
p.exists(), p.suffix, p.name

import json
with open("config.json", encoding="utf-8") as f:
    config = json.load(f)                                  # file -> Python object
with open("out.json", "w", encoding="utf-8") as f:
    json.dump(config, f, indent=2)

# Safe replacement of a file: write a temp file, then swap it in, so a crash never leaves half a file
import os
tmp = "data.txt.tmp"
with open(tmp, "w", encoding="utf-8") as f:
    f.write("new content")
os.replace(tmp, "data.txt")
```

## File Handling in Go

```go
// Small files: one call each
data, err := os.ReadFile("data.txt")                        // []byte
if err != nil {
    return err
}
text := string(data)

err = os.WriteFile("out.txt", []byte("hello"), 0o644)       // creates or truncates; 0o644 = permissions

// Larger files: open, defer Close, read line by line
f, err := os.Open("data.txt")                               // read-only
if err != nil {
    return err
}
defer f.Close()

scanner := bufio.NewScanner(f)
for scanner.Scan() {
    fmt.Println(scanner.Text())
}
if err := scanner.Err(); err != nil {                        // a scanner stops silently: always check Err
    return err
}

// Append / create with flags
out, err := os.OpenFile("app.log", os.O_APPEND|os.O_CREATE|os.O_WRONLY, 0o644)
if err != nil {
    return err
}
defer out.Close()
fmt.Fprintln(out, "one more line")

// Paths and checks
p := filepath.Join("logs", "app.log")                       // portable path building
_, err = os.Stat(p)
if errors.Is(err, fs.ErrNotExist) {
    os.MkdirAll(filepath.Dir(p), 0o755)
}

// JSON file <-> struct
var cfg Config
if err := json.Unmarshal(data, &cfg); err != nil {
    return err
}
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

## TaskGroup, Semaphore, and Error Handling

```python
import asyncio

async def fetch(name):
    await asyncio.sleep(1)
    return name

async def main():
    # TaskGroup (3.11+): if one task fails, the others are cancelled
    async with asyncio.TaskGroup() as tg:
        t1 = tg.create_task(fetch("a"))
        t2 = tg.create_task(fetch("b"))
    print(t1.result(), t2.result())          # both are done after the block

    # gather: collect failures instead of stopping at the first one
    results = await asyncio.gather(fetch("x"), failing(), return_exceptions=True)
    # results == ['x', ValueError(...)]

    # Semaphore: cap concurrency (e.g. max 5 requests at a time)
    sem = asyncio.Semaphore(5)
    async def limited(name):
        async with sem:                      # async with: acquire/release without blocking the loop
            return await fetch(name)
    await asyncio.gather(*(limited(str(i)) for i in range(20)))

asyncio.run(main())
```

* Failures inside a `TaskGroup` surface as an `ExceptionGroup` (catch with `except*`)
* `requests.get()`, `time.sleep()` or heavy computation inside `async def` freezes every task; use an async library or `asyncio.to_thread(...)`

**Key rule:** `async def` marks a coroutine, `await` pauses until it resolves, and `asyncio.run()` starts the event loop. Never use `time.sleep()` inside async code — use `asyncio.sleep()`.

---

# Concurrency and Parallelism

## Concurrency vs Parallelism

* Concurrency means making progress on several tasks during the same period by switching between them. It is about structure: one cook handling three dishes by moving between pots.
* Parallelism means running several tasks at the same instant on different CPU cores. It needs more than one core: three cooks, three dishes.

The kind of work decides what helps:

* I/O-bound work spends its time waiting (network, disk, database). Concurrency helps because the waiting overlaps.
* CPU-bound work spends its time computing (image processing, number crunching, compression). Only real parallelism, meaning more cores doing work, makes it faster.

## Threads, Processes and Async I/O

| Model | Memory | Scheduled by | Best for |
| :--- | :--- | :--- | :--- |
| Thread | Shared with the other threads of the process | Operating system | I/O-bound work with blocking libraries |
| Process | Separate memory for each process | Operating system | CPU-bound work, isolation |
| Async I/O (event loop) | One thread, shared memory | Your code, at each `await` | Very many simultaneous I/O waits |
| Goroutine (Go) | Shared | Go runtime, over a few OS threads | Almost everything, I/O and CPU |

Shared memory is fast and convenient but dangerous: two threads changing the same data need synchronization. Separate processes cannot damage each other's memory, but sharing data between them costs serialization and copying.

## The GIL (Global Interpreter Lock)

CPython has a lock that lets only one thread execute Python bytecode at a time. It protects the interpreter's internals (such as reference counts), which keeps the implementation simple and fast for single-threaded code.

* Threads still help I/O-bound programs: the GIL is released while a thread waits for the network or disk
* Threads do not speed up CPU-bound pure Python code: four threads share one core's worth of Python execution
* C extensions such as NumPy release the GIL during heavy computation, so they can use several cores from threads
* Python 3.13 added an optional "free-threaded" build without the GIL (experimental there, officially supported from 3.14, and not the default build), but libraries need to support it
* Go has nothing like it: goroutines run in parallel on all available cores (`GOMAXPROCS` defaults to the core count)

## Threads in Python

```python
import threading
import time

def worker(name):
    time.sleep(1)                           # blocking wait: the GIL is released
    print(f"{name} finished")

threads = [threading.Thread(target=worker, args=(f"t{i}",)) for i in range(3)]
for t in threads:
    t.start()
for t in threads:
    t.join()                                 # wait for them all: takes about 1s, not 3s
```

```python
# Thread pools are usually nicer than manual threads
from concurrent.futures import ThreadPoolExecutor
import requests

urls = ["https://example.com"] * 10

def fetch(url):
    return requests.get(url, timeout=5).status_code

with ThreadPoolExecutor(max_workers=8) as pool:
    results = list(pool.map(fetch, urls))    # results come back in input order
```

### Daemon Threads, Event, Queue, and as_completed

```python
import threading, queue

# Daemon thread: killed automatically when the main program exits
t = threading.Thread(target=worker, daemon=True)

# Event: a simple on/off signal between threads
ready = threading.Event()
ready.wait()                 # blocks until another thread calls ready.set()

# Queue: thread-safe hand-off (producer/consumer), no manual locks needed
q = queue.Queue()

def producer():
    for i in range(5):
        q.put(i)
    q.put(None)              # sentinel: "no more work"

def consumer():
    while (item := q.get()) is not None:
        print("got", item)

threading.Thread(target=producer).start()
threading.Thread(target=consumer).start()
```

```python
from concurrent.futures import ThreadPoolExecutor, as_completed

with ThreadPoolExecutor(max_workers=4) as pool:
    futures = {pool.submit(fetch, u): u for u in urls}
    for fut in as_completed(futures):          # yields as each finishes, not in input order
        try:
            print(futures[fut], fut.result())   # .result() re-raises the worker's exception
        except Exception as e:
            print(futures[fut], "failed:", e)
```

| Primitive | Use |
| :--- | :--- |
| `Lock` | One thread at a time in a critical section |
| `RLock` | Lock the same thread may acquire again (re-entrant) |
| `Semaphore(n)` | Let at most `n` threads in at once |
| `Event` | One thread signals, others wait |
| `Queue` | Pass data between threads safely |

> An exception inside a plain `Thread` only prints a traceback and is lost; with executors it comes back through `.result()`.

## Race Conditions and Locks

A race condition is a bug where the result depends on the timing of threads. The classic cause is a read-modify-write that is not atomic: `counter += 1` is really three steps (read, add, write), and another thread can run between them, so updates are lost.

```python
import threading

counter = 0
lock = threading.Lock()

def work():
    global counter
    for _ in range(100_000):
        with lock:                           # without the lock, increments can be lost
            counter += 1

threads = [threading.Thread(target=work) for _ in range(4)]
for t in threads: t.start()
for t in threads: t.join()
print(counter)                               # 400000 with the lock
```

Without the lock the bug may or may not show on your machine, which makes it worse: it is still a bug. Safer than sharing and locking is to send data between threads through a `queue.Queue`, which is built to be thread-safe.

## Deadlocks

A deadlock happens when threads wait for each other forever. The usual recipe is two locks taken in opposite order.

```python
lock_a, lock_b = threading.Lock(), threading.Lock()

def task1():
    with lock_a:
        time.sleep(0.1)
        with lock_b:                          # waits for task2 to release lock_b
            ...

def task2():
    with lock_b:
        time.sleep(0.1)
        with lock_a:                          # waits for task1 to release lock_a -> stuck forever
            ...
```

Avoid deadlocks by always acquiring multiple locks in the same global order, holding locks for as short a time as possible, avoiding calling unknown code while holding a lock, and using timeouts (`lock.acquire(timeout=2)`).

## Processes with multiprocessing

Each process has its own interpreter and its own GIL, so CPU-bound work runs truly in parallel. Arguments and results are pickled and copied between processes, which costs time, and a process takes longer to start than a thread.

```python
from concurrent.futures import ProcessPoolExecutor

def heavy(n):
    return sum(i * i for i in range(n))

if __name__ == "__main__":                   # REQUIRED: child processes re-import this file on Windows/macOS
    with ProcessPoolExecutor() as pool:      # defaults to one worker per CPU core
        results = list(pool.map(heavy, [10_000_000] * 4))
    print(results)
```

### Process, Pool, Queue and Shared Values

Processes do not share memory: a global changed in a child is invisible to the parent. Data moves through queues, pipes, or shared objects.

```python
from multiprocessing import Process, Queue, Pool, Value

def square(n):
    return n * n

def worker(q, n):
    q.put(n * n)                       # send a result back to the parent

def add(counter):
    for _ in range(1000):
        with counter.get_lock():       # shared Value has its own lock
            counter.value += 1

if __name__ == "__main__":             # required on Windows/macOS (spawn)
    with Pool(4) as pool:
        print(pool.map(square, range(5)))        # [0, 1, 4, 9, 16]

    q = Queue()
    procs = [Process(target=worker, args=(q, n)) for n in range(3)]
    for p in procs: p.start()
    results = [q.get() for _ in procs]  # read BEFORE join(), or a full queue can block forever
    for p in procs: p.join()
    print(sorted(results))              # order is not guaranteed, so sort

    counter = Value("i", 0)             # shared int
    ps = [Process(target=add, args=(counter,)) for _ in range(4)]
    for p in ps: p.start()
    for p in ps: p.join()
    print(counter.value)                # 4000
```

* Functions and arguments must be picklable, so define worker functions at module top level (no lambdas)
* `Manager()` gives shared dicts and lists, slower but convenient
* `multiprocessing.get_start_method()` shows how children are created (`fork`, `spawn`, `forkserver`)

## When to Use Threading vs Multiprocessing vs asyncio

| Workload | Use | Why |
| :--- | :--- | :--- |
| A few to a few hundred blocking I/O calls (requests, DB driver) | `threading` / `ThreadPoolExecutor` | Simple, the GIL is released while waiting |
| Thousands of simultaneous connections, async libraries available | `asyncio` | Very cheap tasks, no locks needed between awaits |
| CPU-bound pure Python (parsing, math, image work) | `multiprocessing` / `ProcessPoolExecutor` | Bypasses the GIL with separate processes |
| CPU-bound with NumPy or other native libraries | threads or the library's own parallelism | The library releases the GIL |
| Mixed: async program that must call blocking code | `asyncio.to_thread(...)` or `run_in_executor` | Keeps the event loop responsive |

Quick rule: waiting → threads or asyncio, computing → processes. Measure before deciding; the simplest option that is fast enough wins.

```python
import asyncio

def blocking_call(x):
    time.sleep(1)
    return x * 2

async def main():
    result = await asyncio.to_thread(blocking_call, 21)   # runs in a worker thread, event loop stays free
    print(result)

asyncio.run(main())
```

---

# Concurrency in Go

## Goroutines

A goroutine is a function running concurrently with other code, started with `go`. It is far cheaper than an OS thread (it starts with a few kilobytes of stack that grows as needed), and the Go runtime schedules thousands of them over a handful of OS threads, using all cores. When `main` returns, the program exits and every goroutine is killed, so you must wait for them.

```go
func main() {
    var wg sync.WaitGroup
    for i := range 3 {
        wg.Add(1)                    // count one more goroutine to wait for (before starting it)
        go func() {
            defer wg.Done()          // signal completion
            fmt.Println("worker", i)
        }()
    }
    wg.Wait()                        // block until the counter returns to zero
}
```

Newer Go versions (1.25+) also provide `wg.Go(func() { ... })`, which does the Add and Done for you.

## Channels

A channel is a typed pipe between goroutines. Sending and receiving are synchronization points.

```go
ch := make(chan int)                 // unbuffered: a send waits until another goroutine receives
go func() { ch <- 42 }()             // send
v := <-ch                            // receive (blocks until a value arrives)

buf := make(chan string, 3)          // buffered: sends only block when the buffer is full
buf <- "a"
buf <- "b"
fmt.Println(len(buf), cap(buf))      // 2 3

// Producer / consumer
func produce(n int) <-chan int {     // <-chan int: receive-only in the caller's view
    out := make(chan int)
    go func() {
        defer close(out)             // the SENDER closes the channel when it is done
        for i := 0; i < n; i++ {
            out <- i
        }
    }()
    return out
}

for v := range produce(3) {          // range stops when the channel is closed and drained
    fmt.Println(v)
}

v, ok := <-ch                        // ok is false when the channel is closed and empty
```

* Only the sender should close a channel; sending on a closed channel panics
* Receiving from a closed channel returns the zero value immediately (check `ok`)
* Sending to or receiving from a nil channel blocks forever
* `chan<- T` is send-only and `<-chan T` is receive-only; use them in function signatures to state intent

## select

`select` waits on several channel operations and runs the first one that is ready. It is how you add timeouts, cancellation, and multiplexing.

```go
select {
case msg := <-messages:
    fmt.Println("got", msg)
case <-time.After(2 * time.Second):
    fmt.Println("timed out")
case <-ctx.Done():
    fmt.Println("cancelled")
default:                             // optional: run this if nothing is ready right now (non-blocking)
}
```

## Mutexes and Atomics

A mutex lets only one goroutine at a time into a critical section.

```go
type SafeCounter struct {
    mu sync.Mutex                    // put the mutex next to the data it guards
    n  map[string]int
}

func (c *SafeCounter) Inc(key string) {
    c.mu.Lock()
    defer c.mu.Unlock()              // unlock on every path, even a panic
    c.n[key]++
}

var rw sync.RWMutex                  // many readers OR one writer: RLock/RUnlock for reads

var hits atomic.Int64                // lock-free counter for a single number
hits.Add(1)
hits.Load()

var once sync.Once
once.Do(initialize)                  // run exactly once, even when called from many goroutines
```

Never copy a mutex after first use (pass the struct by pointer); `go vet` reports it.

## Channels vs Shared Memory with a Mutex

Go's slogan is "do not communicate by sharing memory; share memory by communicating". Both tools are correct; they fit different jobs.

* Channels move ownership of data or signal an event: pipelines, worker pools, fan-in/fan-out, "work is done", "stop now". Only one goroutine touches a value at a time because it is handed over.
* A mutex protects state that many goroutines read or update in place: counters, caches, maps, a struct's fields. It is simpler and cheaper for that job.
* Channels cost more per operation and can deadlock or leak goroutines when nobody receives; a mutex can be forgotten or held too long.
* If you find yourself building a channel dance just to protect one map, use a mutex. If you are handing work from stage to stage, use channels.

```go
// The same counter, owned by one goroutine that everyone talks to through a channel
type op struct{ key string; reply chan int }

func counterServer(ops <-chan op) {
    counts := map[string]int{}       // only this goroutine ever touches the map
    for o := range ops {
        counts[o.key]++
        o.reply <- counts[o.key]
    }
}
```

## Worker Pool

```go
func worker(jobs <-chan int, results chan<- int, wg *sync.WaitGroup) {
    defer wg.Done()
    for j := range jobs {
        results <- j * j
    }
}

func main() {
    jobs := make(chan int, 10)
    results := make(chan int, 10)

    var wg sync.WaitGroup
    for w := 0; w < 3; w++ {
        wg.Add(1)
        go worker(jobs, results, &wg)
    }

    for i := 1; i <= 5; i++ {
        jobs <- i
    }
    close(jobs)                      // no more jobs: workers leave their range loops
    wg.Wait()
    close(results)

    for r := range results {
        fmt.Println(r)
    }
}
```

## Race Conditions and Data Races

A data race is a specific, precisely defined bug: two goroutines access the same memory at the same time, at least one of them writes, and nothing orders the accesses (no mutex, channel, or atomic). The result is unpredictable, and the Go memory model gives no guarantees. A race condition is broader: the outcome depends on timing, even if every individual access is synchronized (for example, checking a balance under one lock and withdrawing under another).

```go
counter := 0
var wg sync.WaitGroup
for i := 0; i < 1000; i++ {
    wg.Add(1)
    go func() {
        defer wg.Done()
        counter++                    // DATA RACE: read, add, write from many goroutines
    }()
}
wg.Wait()
fmt.Println(counter)                 // often less than 1000
```

Fixes: wrap the update in a mutex, use `atomic.Int64`, or let one goroutine own the counter and send it increments over a channel.

How to detect a data race: run or test with the race detector.

```bash
go run -race .
go test -race ./...
go build -race
```

```text
==================
WARNING: DATA RACE
Write at 0x00c0000a6010 by goroutine 8:
  main.main.func1()
      /app/main.go:14 +0x...

Previous write at 0x00c0000a6010 by goroutine 7:
  main.main.func1()
      /app/main.go:14 +0x...
==================
```

The detector only reports races that actually happen during that run, and it makes the program several times slower and hungrier for memory, so use it in tests and CI with realistic concurrent load, not in production. Writing to a map from two goroutines is detected even without `-race` and crashes with `fatal error: concurrent map writes`.

## Deadlocks in Go

A deadlock is when goroutines wait on each other forever. When every goroutine is blocked, the runtime stops the program:

```text
fatal error: all goroutines are asleep - deadlock!
```

```go
ch := make(chan int)
ch <- 1                              // unbuffered send with no receiver: main blocks forever

var wg sync.WaitGroup
wg.Add(1)
wg.Wait()                            // Done is never called

mu.Lock()
mu.Lock()                            // Go mutexes are not re-entrant: locking twice deadlocks
```

The runtime only notices when everything is stuck. A deadlock among some goroutines while others still run is not reported; it looks like a hang or a goroutine leak. Prevent it with consistent lock ordering, timeouts and context cancellation on every blocking operation, and by making clear who closes each channel. To investigate, send `SIGQUIT` (Ctrl+\) to dump all goroutine stacks, or use pprof's goroutine profile.

## Shared State Guidelines

* Prefer not sharing: give each goroutine its own data, or hand data off through a channel
* If you must share, guard it with one clearly named mutex, document which fields it protects, and keep critical sections small
* Treat shared data that is never modified after creation as safe
* Do not start a goroutine unless you know how and when it will stop
* Run tests with `-race` as a habit

---

# Cancellation and Timeouts

## Cancellation in Python

```python
import asyncio

async def long_job():
    try:
        await asyncio.sleep(60)
    except asyncio.CancelledError:
        print("cleaning up")                 # release resources, then re-raise
        raise

async def main():
    task = asyncio.create_task(long_job())
    await asyncio.sleep(1)
    task.cancel()                            # asks the task to stop at its next await
    try:
        await task
    except asyncio.CancelledError:
        print("cancelled")

    async with asyncio.timeout(2):           # Python 3.11+: cancels the block after 2 seconds
        await long_job()
```

Threads cannot be killed from outside. Use a flag that the thread checks:

```python
import threading

stop = threading.Event()

def worker():
    while not stop.is_set():
        do_a_little_work()

t = threading.Thread(target=worker)
t.start()
stop.set()                                   # ask it to stop
t.join()
```

## context.Context in Go

A `context.Context` carries a cancellation signal, an optional deadline, and request-scoped values through a call chain. When it is cancelled, its `Done()` channel closes, and everything that listens should stop and return `ctx.Err()`.

```go
ctx := context.Background()                          // root context: never cancelled
ctx, cancel := context.WithCancel(ctx)               // cancel manually
ctx, cancel = context.WithTimeout(ctx, 2*time.Second) // cancelled automatically after 2s
ctx, cancel = context.WithDeadline(ctx, time.Now().Add(time.Minute))
defer cancel()                                       // ALWAYS call cancel to release resources

ctx.Err()                                            // nil, context.Canceled, or context.DeadlineExceeded
```

Conventions: `ctx` is the first parameter (`func Fetch(ctx context.Context, id int) error`), you pass it down to everything that can block, and you do not store it in a struct.

```go
func worker(ctx context.Context, jobs <-chan int) error {
    for {
        select {
        case <-ctx.Done():                           // told to stop
            return ctx.Err()
        case j, ok := <-jobs:
            if !ok {
                return nil                           // no more work
            }
            process(j)
        }
    }
}

// Outgoing HTTP call that obeys the context
req, err := http.NewRequestWithContext(ctx, http.MethodGet, url, nil)
resp, err := http.DefaultClient.Do(req)              // returns early if ctx is cancelled
// database: db.QueryContext(ctx, "SELECT ...")
```

## Stopping Work When an HTTP Request Is Cancelled

Each incoming request carries a context. `r.Context()` is cancelled when the client disconnects, when the request is cancelled, or when the handler returns. Pass it to every slow operation, and those operations stop on their own.

```go
func handler(w http.ResponseWriter, r *http.Request) {
    ctx, cancel := context.WithTimeout(r.Context(), 2*time.Second)   // also enforce our own deadline
    defer cancel()

    result, err := slowQuery(ctx)
    switch {
    case errors.Is(err, context.Canceled):
        return                                                       // the client is gone: nobody to answer
    case errors.Is(err, context.DeadlineExceeded):
        http.Error(w, "timeout", http.StatusGatewayTimeout)
        return
    case err != nil:
        http.Error(w, err.Error(), http.StatusInternalServerError)
        return
    }
    fmt.Fprintln(w, result)
}

func slowQuery(ctx context.Context) (string, error) {
    select {
    case <-time.After(5 * time.Second):                              // stands in for slow work
        return "done", nil
    case <-ctx.Done():
        return "", ctx.Err()                                         // stop immediately
    }
}
```

In a loop doing CPU work, check the context between iterations (`if ctx.Err() != nil { return ctx.Err() }`). A goroutine that ignores its context cannot be stopped and will leak.

## Graceful Shutdown

```go
func main() {
    ctx, stop := signal.NotifyContext(context.Background(), os.Interrupt, syscall.SIGTERM)
    defer stop()

    srv := &http.Server{Addr: ":8080", Handler: mux}
    go func() {
        if err := srv.ListenAndServe(); err != nil && !errors.Is(err, http.ErrServerClosed) {
            log.Fatal(err)
        }
    }()

    <-ctx.Done()                                                     // wait for Ctrl+C or SIGTERM

    shutdownCtx, cancel := context.WithTimeout(context.Background(), 10*time.Second)
    defer cancel()
    srv.Shutdown(shutdownCtx)                                        // stop accepting, let in-flight requests finish
}
```

---

# Structs, Methods and Interfaces in Go

Go has no classes and no inheritance. It models data with structs, behavior with methods, abstraction with interfaces, and code reuse with composition. The Python OOP section below covers the class-based version of the same ideas.

## Structs and Methods

```go
type Car struct {
    Brand string
    Model string
    wheels int                       // lowercase field: private to the package
}

func NewCar(brand, model string) *Car {          // constructor by convention: a function named NewX
    return &Car{Brand: brand, Model: model, wheels: 4}
}

func (c Car) Info() string {                      // method: a function with a receiver
    return c.Brand + " " + c.Model
}

car := NewCar("Toyota", "Corolla")
fmt.Println(car.Info())                           // Toyota Corolla

p := Car{Brand: "Honda"}                          // unset fields get zero values
q := Car{"Honda", "Civic", 4}                     // positional literal (fragile, avoid for big structs)
```

## Pointer Receivers

A method receives the value it is called on as a copy (value receiver) or as a pointer (pointer receiver).

```go
type Counter struct{ n int }

func (c Counter) IncValue()  { c.n++ }            // value receiver: works on a COPY, caller sees no change
func (c *Counter) Inc()      { c.n++ }            // pointer receiver: changes the original

var c Counter
c.IncValue()
fmt.Println(c.n)                                  // 0
c.Inc()                                           // Go takes &c for you because c is addressable
fmt.Println(c.n)                                  // 1
```

When should a method use a pointer receiver?

* The method modifies the receiver
* The struct is large and copying it on every call would be wasteful
* The struct contains something that must not be copied (`sync.Mutex`, `sync.WaitGroup`, a buffer)
* Consistency: if any method needs a pointer receiver, use pointer receivers for all methods of that type
* Use value receivers for small, immutable-style types (`time.Time`, small structs, named basic types like `type Celsius float64`)

## Composition with Embedding

Embedding a type in a struct promotes its fields and methods to the outer type. This is "has-a" that reads like "is-a", but there is no inheritance: the embedded value is a normal field you can also reach by name.

```go
type Logger struct{ prefix string }

func (l Logger) Log(msg string) { fmt.Println(l.prefix + msg) }

type Server struct {
    Logger                                         // embedded: Server gets Log()
    Addr string
}

s := Server{Logger: Logger{prefix: "[srv] "}, Addr: ":8080"}
s.Log("starting")                                  // promoted method; same as s.Logger.Log(...)

// The outer type can define its own method with the same name (it shadows the embedded one)
func (s Server) Log(msg string) { s.Logger.Log("server: " + msg) }
```

Embedding does not give polymorphism by itself: the embedded type's methods run with the embedded value as receiver and know nothing about the outer type. Use interfaces for polymorphism.

## Interfaces

An interface is a set of method signatures. In Go, a type implements an interface simply by having those methods: there is no `implements` keyword. This is called implicit (structural) satisfaction, and the compiler checks it. It is like Python's duck typing, but verified at compile time.

```go
type Notifier interface {
    Send(message string) error
}

type EmailNotifier struct{ To string }

func (e EmailNotifier) Send(message string) error {
    fmt.Println("email to", e.To+":", message)
    return nil
}

type SlackNotifier struct{ Channel string }

func (s SlackNotifier) Send(message string) error {
    fmt.Println("slack", s.Channel+":", message)
    return nil
}

func alert(n Notifier, msg string) error {         // works with ANY type that has Send(string) error
    return n.Send(msg)
}

alert(EmailNotifier{To: "ops@example.com"}, "deploy done")
alert(SlackNotifier{Channel: "#ops"}, "deploy done")
```

How does a type implement an interface in Go? By defining every method of the interface with the exact names and signatures. The type does not need to know the interface exists, so interfaces can be defined by the code that uses them, even for types from other packages.

Guidelines:

* Keep interfaces small (one to three methods); the standard library's `io.Reader`, `io.Writer`, `fmt.Stringer`, and `error` have one method each
* Define the interface where it is used (the consumer), not next to the implementation
* "Accept interfaces, return concrete types"
* A compile-time check that a type satisfies an interface: `var _ Notifier = (*EmailNotifier)(nil)`

## Interface Satisfaction and Pointer Receivers

The methods a type has decide which interfaces it satisfies. A value of type `T` has only the value-receiver methods; a `*T` has both value and pointer receiver methods.

```go
type Speaker interface{ Speak() string }

type Dog struct{}
func (d *Dog) Speak() string { return "Woof" }     // pointer receiver

// var s Speaker = Dog{}      // compile error: Dog does not implement Speaker (method Speak has pointer receiver)
var s Speaker = &Dog{}        // ok: *Dog has Speak
```

## Empty Interface, Type Assertions and Type Switches

```go
var x any = "hello"                 // any = interface{}: holds a value of any type

s, ok := x.(string)                 // type assertion, safe form
n := x.(int)                        // panics because x holds a string

switch v := x.(type) {              // type switch
case int:
    fmt.Println("int", v+1)
case string:
    fmt.Println("string", strings.ToUpper(v))
case nil:
    fmt.Println("nothing")
default:
    fmt.Printf("other %T\n", v)
}
```

An interface value internally holds a (type, value) pair, which is why a nil `*T` stored in an interface is not a nil interface (see None, nil, and NULL).

## How Go Differs from Class-Based OOP

* No classes: data (struct) and behavior (methods) are declared separately
* No inheritance: compose with embedding, abstract with interfaces
* No method overloading and no operator overloading
* No constructors or destructors: use `NewX` functions and `defer`/`Close()`
* Visibility is by capitalization of the name, not by `public` / `private` keywords

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

### DevOps/Cloud Example

```python
class EC2Instance:
    def __init__(self, instance_id, instance_type, region):
        self.instance_id = instance_id
        self.instance_type = instance_type
        self.region = region

    def info(self):
        return f"{self.instance_id} ({self.instance_type}) in {self.region}"

server = EC2Instance("i-0abc123", "t2.micro", "us-east-1")
print(server.info())
```

## Inheritance

Inheritance is a core concept in Object-Oriented Programming (OOP) that allows a new class (child/subclass) to acquire the properties, attributes, and behaviors (methods) of an existing class (parent/superclass). It models an "IS-A" relationship between classes—for example, a Car is a Vehicle.

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

### DevOps/Cloud Example

```python
class CloudResource:
    def __init__(self, name):
        self.name = name

    def deploy(self):
        return f"Deploying {self.name}"

class Container(CloudResource):
    def __init__(self, name, image):
        super().__init__(name)
        self.image = image

    def deploy(self):
        return f"{super().deploy()} using image {self.image}"

print(Container("api-service", "nginx:latest").deploy())
```

### Types of Inheritance

```python
# 1. Single Inheritance
class Server:
    def start(self):
        return "Server starting"

class WebServer(Server):
    pass

# 2. Multilevel Inheritance
class BaseResource:
    def tag(self):
        return "tagged"

class VirtualMachine(BaseResource):
    pass

class GPUInstance(VirtualMachine):
    pass

# 3. Hierarchical Inheritance (one parent, many children)
class CloudService:
    def billing(self):
        return "billed monthly"

class Storage(CloudService):
    pass

class Compute(CloudService):
    pass

# 4. Multiple Inheritance (one child, many parents)
class Loggable:
    def log(self):
        return "logging enabled"

class Scalable:
    def scale(self):
        return "auto-scaling enabled"

class Microservice(Loggable, Scalable):
    pass

svc = Microservice()
print(svc.log(), svc.scale())

# 5. Hybrid Inheritance (combination of the above patterns)
class Monitored:
    def monitor(self):
        return "monitoring active"

class Deployable(CloudService, Monitored):
    pass
```

**Quick rule:** Python resolves multiple/hybrid inheritance using MRO (Method Resolution Order) — check with `ClassName.__mro__`.

## Method Overriding

Overriding = a subclass redefines a method that already exists in its parent class, with the **same name and signature**, to change its behavior.

```python
class Notifier:
    def send(self, message):
        return f"Generic notification: {message}"

class EmailNotifier(Notifier):
    def send(self, message):   # overrides parent method
        return f"Email sent: {message}"

class SlackNotifier(Notifier):
    def send(self, message):   # overrides parent method
        return f"Slack alert: {message}"

for notifier in [EmailNotifier(), SlackNotifier()]:
    print(notifier.send("Deployment successful"))
```

### DevOps/Cloud Example

```python
class DeploymentStrategy:
    def deploy(self):
        return "Standard deployment"

class BlueGreenDeployment(DeploymentStrategy):
    def deploy(self):
        return "Blue-Green deployment: switching traffic to new environment"

class CanaryDeployment(DeploymentStrategy):
    def deploy(self):
        return "Canary deployment: rolling out to 10% of traffic first"

print(BlueGreenDeployment().deploy())
print(CanaryDeployment().deploy())
```

## Method Overloading

Python doesn't support true method overloading (same method name, different parameter lists) like Java/C++. It's simulated using **default arguments**, `*args`, or the `functools.singledispatch` decorator.

```python
# Simulated with default arguments
class Calculator:
    def add(self, a, b, c=0):
        return a + b + c

calc = Calculator()
print(calc.add(2, 3))          # 5
print(calc.add(2, 3, 4))          # 9
```

```python
# Simulated with *args
class Calculator:
    def add(self, *args):
        return sum(args)

calc = Calculator()
print(calc.add(1, 2))          # 3
print(calc.add(1, 2, 3, 4))       # 10
```

### DevOps/Cloud Example

```python
from functools import singledispatch

@singledispatch
def scale_resource(resource):
    raise NotImplementedError("Unsupported resource type")

@scale_resource.register
def _(resource: int):
    return f"Scaling {resource} EC2 instances"

@scale_resource.register
def _(resource: str):
    return f"Scaling service: {resource}"

print(scale_resource(5))              # Scaling 5 EC2 instances
print(scale_resource("api-gateway"))     # Scaling service: api-gateway
```

## Polymorphism

Polymorphism in Object-Oriented Programming (OOP) means "many forms," allowing a single function, method, or operator to behave differently depending on the object or context it is used with

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

### DevOps/Cloud Example

```python
class AWSDeployer:
    def deploy(self):
        return "Deploying to AWS via CloudFormation"

class AzureDeployer:
    def deploy(self):
        return "Deploying to Azure via ARM templates"

class GCPDeployer:
    def deploy(self):
        return "Deploying to GCP via Terraform"

for deployer in [AWSDeployer(), AzureDeployer(), GCPDeployer()]:
    print(deployer.deploy())   # same interface, different behavior
```

## Encapsulation

Encapsulation is a core principle of object-oriented programming (OOP) that bundles data (attributes) and the methods (functions) that operate on that data into a single unit, usually known as a class. It acts as a protective shield that prevents external code from directly accessing or modifying the internal state of an object.

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

### DevOps/Cloud Example

```python
class DatabaseConnection:
    def __init__(self, password):
        self.__password = password   # private, hidden from outside access

    def connect(self):
        return f"Connecting with password: {'*' * len(self.__password)}"

db = DatabaseConnection("s3cr3t")
print(db.connect())          # Connecting with password: *******
# db.__password              # AttributeError — not directly accessible
```

## Property Decorator

`@property` lets a method be accessed like an attribute — useful for validation, computed values, or controlled read/write access without changing the public interface.

```python
class Circle:
    def __init__(self, radius):
        self._radius = radius

    @property
    def radius(self):          # getter
        return self._radius

    @radius.setter
    def radius(self, value):   # setter with validation
        if value < 0:
            raise ValueError("Radius cannot be negative")
        self._radius = value

    @property
    def area(self):            # computed/read-only property
        return 3.14 * self._radius ** 2

c = Circle(5)
print(c.radius)     # 5   (accessed like an attribute, not c.radius())
print(c.area)          # 78.5
c.radius = 10           # uses the setter
```

### DevOps/Cloud Example

```python
class ServerConfig:
    def __init__(self, cpu_cores):
        self._cpu_cores = cpu_cores

    @property
    def cpu_cores(self):
        return self._cpu_cores

    @cpu_cores.setter
    def cpu_cores(self, value):
        if value < 1:
            raise ValueError("A server needs at least 1 CPU core")
        self._cpu_cores = value

    @property
    def instance_size(self):
        return "large" if self._cpu_cores >= 8 else "small"

server = ServerConfig(4)
print(server.instance_size)   # small
server.cpu_cores = 16
print(server.instance_size)      # large
```

## Abstraction

Abstraction in Object-Oriented Programming (OOP) is the process of hiding internal implementation details and showing only essential features to the user. It focuses on what an object does rather than how it does it.

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

### DevOps/Cloud Example

```python
from abc import ABC, abstractmethod

class CloudProvisioner(ABC):
    @abstractmethod
    def provision(self):
        pass

class TerraformProvisioner(CloudProvisioner):
    def provision(self):
        return "Running terraform apply"

class AnsibleProvisioner(CloudProvisioner):
    def provision(self):
        return "Running ansible-playbook"

for tool in [TerraformProvisioner(), AnsibleProvisioner()]:
    print(tool.provision())
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

### DevOps/Cloud Example

```python
class Deployment:
    active_deployments = 0

    def __init__(self, name):
        self.name = name
        Deployment.active_deployments += 1

    @classmethod
    def total_active(cls):
        return f"{cls.active_deployments} deployments currently active"

    @staticmethod
    def is_valid_env(env):
        return env in ("dev", "staging", "prod")

Deployment("api-v1")
Deployment("worker-v2")
print(Deployment.total_active())            # 2 deployments currently active
print(Deployment.is_valid_env("prod"))         # True
```

## Association

The weakest relationship — two independent classes interact with each other, but neither owns the other and both can exist without the other.

```python
class Developer:
    def __init__(self, name):
        self.name = name

class Repository:
    def __init__(self, name):
        self.name = name

def commit_code(developer, repository):
    return f"{developer.name} pushed code to {repository.name}"

dev = Developer("Alice")
repo = Repository("backend-service")
print(commit_code(dev, repo))   # both objects exist independently
```

## Aggregation

A "has-a" relationship where one class contains another, but the contained object can exist independently (weak ownership — no lifecycle dependency).

```python
class Engineer:
    def __init__(self, name):
        self.name = name

class Team:
    def __init__(self, name, engineers):
        self.name = name
        self.engineers = engineers   # Team holds Engineers, but doesn't own their lifecycle

alice = Engineer("Alice")
bob = Engineer("Bob")

devops_team = Team("DevOps", [alice, bob])
print([e.name for e in devops_team.engineers])

# Engineers still exist even if the team is deleted
del devops_team
print(alice.name)   # still valid
```

### DevOps/Cloud Example

```python
class LoadBalancer:
    def __init__(self, name):
        self.name = name

class ServerFarm:
    def __init__(self, load_balancers):
        self.load_balancers = load_balancers   # shared/reusable across farms

lb1 = LoadBalancer("lb-east")
farm = ServerFarm([lb1])
# lb1 can be reused by another farm independently
```

## Composition

A stronger "has-a" relationship where the contained object's lifecycle depends entirely on the container — if the container is destroyed, so are its parts.

```python
class Engine:
    def __init__(self, horsepower):
        self.horsepower = horsepower

class Car:
    def __init__(self, brand):
        self.brand = brand
        self.engine = Engine(300)   # Engine is created and owned by Car

    def start(self):
        return f"{self.brand} starting with {self.engine.horsepower}hp engine"

car = Car("Tesla")
print(car.start())
# If 'car' is deleted, its 'engine' has no independent existence
```

### DevOps/Cloud Example

```python
class Container:
    def __init__(self, image):
        self.image = image

class Pod:
    def __init__(self, name, image):
        self.name = name
        self.container = Container(image)   # Container's lifecycle is bound to the Pod

    def status(self):
        return f"Pod '{self.name}' running image '{self.container.image}'"

pod = Pod("api-pod", "nginx:latest")
print(pod.status())
# Deleting the Pod deletes its Container too — strong ownership
```

**Quick rule:**
- **Association** → "uses-a" (independent, loosely connected)
- **Aggregation** → "has-a" (weak ownership, parts can outlive the whole)
- **Composition** → "owns-a" (strong ownership, parts die with the whole)

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

## Iterables vs Iterators

* An iterable is anything you can loop over: it has `__iter__()` that returns an iterator (lists, tuples, strings, dicts, sets, files, ranges)
* An iterator is the object that produces the values one at a time: it has `__next__()` that returns the next value and raises `StopIteration` when finished, and its `__iter__()` returns itself
* An iterable can be looped over many times (each loop gets a fresh iterator); an iterator is used up after one pass

```python
nums = [1, 2, 3]            # iterable (a list is NOT an iterator)
it = iter(nums)             # get an iterator
next(it)                    # 1
next(it)                    # 2
next(it)                    # 3
next(it)                    # StopIteration

# This is what a for loop does behind the scenes
it = iter(nums)
while True:
    try:
        item = next(it)
    except StopIteration:
        break
    print(item)

it = iter(nums)
list(it)                    # [1, 2, 3]
list(it)                    # []   the iterator is exhausted
```

## Generators and yield

A function that contains `yield` is a generator function. Calling it does not run the body; it returns a generator, which is an iterator. Each `next()` runs the body until the next `yield`, hands out that value, and pauses with all local variables intact. The values are produced lazily, one at a time, so memory use stays small.

```python
def count_up(limit):
    print("start")
    n = 1
    while n <= limit:
        yield n                # pause here and give n to the caller
        n += 1
    print("finished")

gen = count_up(2)              # nothing printed yet: the body has not started
next(gen)                      # prints "start", returns 1
next(gen)                      # returns 2
next(gen)                      # prints "finished", raises StopIteration
```

```python
# Infinite sequence: impossible with a list, trivial with a generator
def fibonacci():
    a, b = 0, 1
    while True:
        yield a
        a, b = b, a + b

from itertools import islice
list(islice(fibonacci(), 10))                 # [0, 1, 1, 2, 3, 5, 8, 13, 21, 34]

# Process a huge file without loading it
def long_lines(path):
    with open(path) as f:
        for line in f:
            if len(line) > 80:
                yield line

# Generator expression: like a list comprehension, but lazy
total = sum(x * x for x in range(1_000_000))  # no million-item list in memory

# yield from delegates to another iterable
def chain_two(a, b):
    yield from a
    yield from b
```

Generators are single use. Once consumed they are empty, so keep the function around and call it again to start over. `itertools` (`islice`, `chain`, `count`, `groupby`, `product`) provides ready-made building blocks for lazy pipelines.

## Iteration in Go

`range` iterates over slices, arrays, strings, maps, channels, and integers. Since Go 1.23 it also works over functions, which makes custom iterators possible (the equivalent of generators).

```go
for i, v := range []string{"a", "b"} {}      // index, value
for i := range 3 {}                           // 0 1 2
for r := range ch {}                          // receives from a channel until it is closed

// Custom iterator: a function that calls yield for each value (Go 1.23+)
func Countdown(n int) iter.Seq[int] {
    return func(yield func(int) bool) {
        for i := n; i > 0; i-- {
            if !yield(i) {                    // yield returns false when the loop used break
                return
            }
        }
    }
}

for v := range Countdown(3) {
    fmt.Println(v)                            // 3 2 1
}

// Helpers in the standard library
keys := slices.Sorted(maps.Keys(m))           // sorted map keys
for _, v := range slices.Values(items) {}
```

Before range-over-func, a "generator" in Go was a goroutine that sends on a channel (see the producer example in Concurrency in Go). That works, but if the consumer stops early the goroutine stays blocked forever, so give it a `context` or `done` channel to quit.

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

## Generics in Go

Go has had type parameters since 1.18. Unlike Python's hints, they are enforced by the compiler.

```go
// A type parameter list in square brackets; "any" means no restriction
func First[T any](items []T) T {
    return items[0]
}
First([]int{1, 2, 3})            // 1   T is inferred from the argument
First([]string{"a", "b"})        // "a"

// A constraint limits which types are allowed
func Max[T cmp.Ordered](a, b T) T {          // cmp.Ordered: integers, floats, strings
    if a > b {
        return a
    }
    return b
}

type Number interface {
    ~int | ~int64 | ~float64                 // a union; ~ also accepts types defined from these
}

func Sum[T Number](nums []T) T {
    var total T
    for _, n := range nums {
        total += n
    }
    return total
}

// Generic helpers Go does not ship as built-ins
func Map[T, U any](items []T, f func(T) U) []U {
    out := make([]U, 0, len(items))
    for _, it := range items {
        out = append(out, f(it))
    }
    return out
}

// Generic type
type Stack[T any] struct{ items []T }

func (s *Stack[T]) Push(item T) { s.items = append(s.items, item) }
func (s *Stack[T]) Pop() T {
    last := s.items[len(s.items)-1]
    s.items = s.items[:len(s.items)-1]
    return last
}

var st Stack[int]
st.Push(1)
```

`comparable` is the built-in constraint for types that support `==`, which is what a generic map key needs (`func Keys[K comparable, V any](m map[K]V) []K`). Reach for generics when you write the same logic for several types; for a single type or a behavior difference, a plain function or an interface is simpler.

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

# GUI Basics: tkinter Canvas

## find_overlapping and Related Search Methods

`canvas.find_overlapping(x1, y1, x2, y2)` returns a tuple of IDs of every canvas item that touches the rectangle (top-left `x1,y1`, bottom-right `x2,y2`). Partial overlap counts. It returns `()` when nothing is found.

```python
import tkinter as tk

root = tk.Tk()
canvas = tk.Canvas(root, width=400, height=300, bg="white")
canvas.pack()

player = canvas.create_rectangle(50, 50, 90, 90, fill="blue")    # ID 1
wall   = canvas.create_rectangle(80, 80, 200, 120, fill="red")   # ID 2
coin   = canvas.create_oval(300, 200, 320, 220, fill="gold")     # ID 3

print(canvas.find_overlapping(80, 80, 100, 100))   # (1, 2): both touch that area
print(canvas.find_overlapping(0, 0, 10, 10))       # ()
```

Collision check (the item always overlaps itself, so remove it from the result):

```python
def collisions(item):
    x1, y1, x2, y2 = canvas.bbox(item)          # bbox works for every item type
    return [i for i in canvas.find_overlapping(x1, y1, x2, y2) if i != item]

if collisions(player):
    print("player hit something")
```

Click detection (a zero-size rectangle is a point):

```python
def on_click(event):
    x, y = canvas.canvasx(event.x), canvas.canvasy(event.y)   # needed if the canvas scrolls
    hit = canvas.find_overlapping(x, y, x, y)
    if hit:
        print("clicked item", hit[-1])          # last ID = topmost item

canvas.bind("<Button-1>", on_click)
```

| Method | Returns |
| :--- | :--- |
| `find_overlapping(x1,y1,x2,y2)` | Items touching the rectangle, even partly |
| `find_enclosed(x1,y1,x2,y2)` | Items completely inside the rectangle |
| `find_closest(x, y)` | The single item nearest to the point |
| `find_withtag(tag)` | Items with that tag (`"all"`, `"current"` also work) |
| `find_all()` | Every item, in stacking order (lowest first) |

> `bbox` can be a pixel or so larger than the drawn shape, so items that just barely touch may be reported as overlapping.

---

# Tooling and Testing

## Debugging

```python
print(f"{value=}")             # f"{x=}" prints the name and the value: value=42
breakpoint()                    # drops into the pdb debugger at this line
```

```text
pdb commands:  n (next line)  s (step into)  c (continue)  p expr (print)  l (list code)  q (quit)
```

IDEs (VS Code, PyCharm, GoLand) provide the same with breakpoints you click. For Go use the Delve debugger (`dlv debug`, `dlv test`), which VS Code and GoLand use behind the scenes.

## Logging

Prefer logging over `print` in anything that runs unattended: it has levels, timestamps, and destinations.

```python
import logging

logging.basicConfig(
    level=logging.INFO,
    format="%(asctime)s %(levelname)s %(name)s: %(message)s",
)
log = logging.getLogger(__name__)

log.debug("noisy detail")                  # hidden at INFO level
log.info("server started on port %d", 8080)
log.warning("disk almost full")
log.error("payment failed for order %s", 123)
log.exception("unexpected failure")        # inside except: also logs the traceback
```

```go
import "log/slog"

slog.Info("server started", "port", 8080)
slog.Warn("disk almost full", "free_mb", 120)
slog.Error("payment failed", "order", 123, "err", err)
// JSON output for log collectors:
logger := slog.New(slog.NewJSONHandler(os.Stdout, nil))
```

Levels, from least to most severe: DEBUG, INFO, WARNING, ERROR, CRITICAL. Never log passwords, tokens, or personal data.

## Testing in Python

Tests are small programs that check your code and run automatically. `pytest` is the usual tool (`pip install pytest`); `unittest` is built in.

```python
# file: mathutils.py
def divide(a, b):
    if b == 0:
        raise ValueError("division by zero")
    return a / b

# file: test_mathutils.py   (files and functions starting with test are discovered automatically)
import pytest
from mathutils import divide

def test_divide():
    assert divide(10, 2) == 5

def test_divide_by_zero():
    with pytest.raises(ValueError):
        divide(1, 0)

@pytest.mark.parametrize("a, b, expected", [(6, 3, 2), (9, 3, 3), (0, 5, 0)])
def test_many(a, b, expected):
    assert divide(a, b) == expected
```

```bash
pytest                          # run everything
pytest -v                       # verbose
pytest -k divide                # only tests whose name matches
pytest --cov=myapp              # coverage report (pytest-cov plugin)
```

## Testing in Go

Testing is built into the `go` tool. Test files end in `_test.go`, test functions are named `TestXxx` and take a `*testing.T`.

```go
// file: mathutils_test.go
package mathutils

import "testing"

func TestDivide(t *testing.T) {
    got, err := Divide(10, 2)
    if err != nil {
        t.Fatalf("unexpected error: %v", err)
    }
    if got != 5 {
        t.Errorf("Divide(10, 2) = %v, want 5", got)
    }
}

// Table-driven test: the idiomatic Go style
func TestDivideTable(t *testing.T) {
    tests := []struct {
        name    string
        a, b    float64
        want    float64
        wantErr bool
    }{
        {"simple", 6, 3, 2, false},
        {"zero divisor", 1, 0, 0, true},
    }
    for _, tc := range tests {
        t.Run(tc.name, func(t *testing.T) {
            got, err := Divide(tc.a, tc.b)
            if (err != nil) != tc.wantErr {
                t.Fatalf("err = %v, wantErr %v", err, tc.wantErr)
            }
            if got != tc.want {
                t.Errorf("got %v, want %v", got, tc.want)
            }
        })
    }
}

func BenchmarkDivide(b *testing.B) {         // benchmarks: go test -bench .
    for i := 0; i < b.N; i++ {
        Divide(10, 2)
    }
}
```

```bash
go test ./...                   # all packages in the module
go test -v -run TestDivide      # verbose, only matching tests
go test -race ./...             # with the data race detector
go test -cover ./...            # coverage
go test -bench . -benchmem      # benchmarks with memory stats
```

## Formatting, Linting and Type Checking

* Python formatting: `black` or `ruff format`; style guide: PEP 8
* Python linting: `ruff` (fast, replaces flake8 and more) or `pylint`
* Python type checking: `mypy` or `pyright`
* Go formatting: `gofmt` / `go fmt ./...`; there is one standard style and every Go project uses it, so formatting is never debated
* Go checks: `go vet ./...` (suspicious code), `staticcheck` or `golangci-lint` (many linters in one)

```bash
# Python
ruff format .
ruff check .
mypy src/

# Go
go fmt ./...
go vet ./...
golangci-lint run
```

Run formatting, linting, type checks, and tests automatically in CI (and optionally in a pre-commit hook) so problems never reach the main branch.

## Everyday Go Commands

```bash
go run .                 # compile and run the main package
go build ./...           # compile everything (checks that it builds), no output file for libraries
go build -o app .        # produce a named executable
go test ./...            # run all tests
go test -race ./...      # tests with race detection
go fmt ./...             # format
go vet ./...             # static checks
go mod tidy              # sync go.mod/go.sum with the imports in your code
go get pkg@version       # add or change a dependency
go install pkg@latest    # install a Go tool into $GOPATH/bin
go doc strings.Split     # documentation in the terminal
go env                   # show the Go environment settings
go version
```

## Measuring Performance

```python
import timeit
timeit.timeit("sum(range(1000))", number=10_000)      # time a snippet

import cProfile
cProfile.run("main()")                                  # which functions take the time
```

```bash
python -m cProfile -s cumtime app.py                    # sort by cumulative time
go test -bench . -cpuprofile cpu.out && go tool pprof cpu.out
```

Measure first, then optimize the part that is actually slow; guessing is usually wrong.

---

# Data Structures and Algorithms with Coding Patterns (Problem-Solving Templates)

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

## Circular array

### Basic circular traversal pattern

Use Modulo to access repeating cycle of numbers

0 % 5 = 0; 1 % 5 = 1; 2 % 5 = 2; 3 % 5 = 3; 4 % 5 = 4;
5 % 5 = 0; 6 % 5 = 1; 7 % 5 = 2; 8 % 5 = 3; 9 % 5 = 4; 
10 % 5 = 0;

```python
arr = [10, 20, 30, 40, 50]
n = len(arr)

i = 0

for _ in range(n):
    print(arr[i])
    i = (i + 1) % n
```

### Starting from any index

```python
arr = [10, 20, 30, 40, 50]
n = len(arr)

start = 2
i = start

for _ in range(n):
    print(arr[i])
    i = (i + 1) % n
```

### Lock Defuse

```python
# Input: code = [5,7,1,4], k = 3; code = [2,4,9,3], k = -2
# Output: [12,10,16,13]; [12,5,6,13]
# Explanation: Each number is replaced by the sum of the next 3 numbers. The decrypted code is [7+1+4, 1+4+5, 4+5+7, 5+7+1].
# Explanation: The decrypted code is [3+9, 2+3, 4+2, 9+4]. If k is negative, the sum is of the previous numbers.

def decrypt(code, k):
    n = len(code)
    arr = [0] * n

    if k == 0:
        return arr

    for i in range(n):
        if k > 0:
            for j in range(1, k + 1):
                arr[i] += code[(i + j) % n]

        else:
            for j in range(1, abs(k) + 1):
                arr[i] += code[(i - j) % n]

    return arr

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

### Maximum Length Substring

```python
def maximumLengthSubstring(self, s: str) -> int:
    maximum = 0

    left = 0
    freq = {}

    for right in range(0, len(s)):
        freq[s[right]] = freq.get(s[right], 0) + 1
        
        # if freq[s[right]] > 2: is wrong Because it removes only one character
        while freq[s[right]] > 2:
            freq[s[left]] -= 1
            left += 1

        maximum = max(maximum, right - left + 1)

    return maximum
```

### Minimum Difference

```python
def minimumDifference(self, nums: List[int], k: int) -> int:
    minimum = float('inf')

    if len(nums) <= 1:
        return 0

    nums.sort()

    for i in range(0, len(nums)-k+1):
        # nums[i:k] is list not integer
        difference = nums[i+k-1] - nums[i]

        minimum = min(minimum, difference) 

    return minimum
```

### Minimum Required

```python
def minimumRecolors(self, nums: str, k: int) -> int:
   minimum = float('inf')


   # Iterate through the string with a sliding window of size k
   for right in range(0, len(nums) - k + 1):
       count = 0
       # Count the number in the current window
       for i in range(right, right+k):
           if nums[i] == 'match':
               count += 1


       minimum = min(minimum, count)
      
   return minimum
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

### Breadth-First Search (BFS)

**Idea:** Visit all immediate neighbors first, then move outward level by level. BFS typically uses a **queue (FIFO)**.

### Traversal levels

``` mermaid
flowchart TB
    A((A)):::l0
    B((B)):::l1
    C((C)):::l1
    D((D)):::l2
    E((E)):::l2
    F((F)):::l2
    G((G)):::l2

    A --- B
    A --- C
    B --- D
    B --- E
    C --- F
    C --- G

    classDef l0 fill:#f9d976,stroke:#8a6d1d,color:#222
    classDef l1 fill:#9ed8f5,stroke:#287da8,color:#222
    classDef l2 fill:#b7e4c7,stroke:#2d6a4f,color:#222
```

### Visit order

**A → B → C → D → E → F → G**

    Step Visit   Queue after adding unvisited neighbors
  ------ ------- ----------------------------------------
       1 A       B, C
       2 B       C, D, E
       3 C       D, E, F, G
       4 D       E, F, G
       5 E       F, G
       6 F       G
       7 G       Empty

### Depth-First Search (DFS)

**Idea:** Follow one branch as far as possible, then backtrack. DFS uses
a **stack (LIFO)** or recursion.

### One possible DFS path

``` mermaid
flowchart TB
    A((1. A)) --> B((2. B))
    B --> D((3. D))
    D -. backtrack .-> B
    B --> E((4. E))
    E -. backtrack .-> A
    A --> C((5. C))
    C --> F((6. F))
    F -. backtrack .-> C
    C --> G((7. G))
```

Dashed arrows indicate backtracking conceptually; the exact drawing is a
traversal illustration, not the graph's full edge set.

### Visit order

**A → B → D → E → C → F → G**

The exact DFS order depends on the order in which neighbors are
considered.

### BFS vs DFS at a glance

  -----------------------------------------------------------------------
  Feature                 BFS                     DFS
  ----------------------- ----------------------- -----------------------
  Strategy                Explore level by level  Explore deeply, then
                                                  backtrack

  Main data structure     Queue (FIFO)            Stack (LIFO) or
                                                  recursion

  Finds shortest path in  Yes                     Not guaranteed
  an unweighted graph?                            

  Common uses             Shortest paths, levels, Backtracking, cycle
                          nearest target          detection, connected
                                                  components

  Time complexity         `O(V + E)`              `O(V + E)`

  Space complexity        `O(V)` worst case       `O(V)` worst case
  -----------------------------------------------------------------------

Here, `V` is the number of vertices and `E` is the number of edges.

### Memory trick

-   **BFS = Broad first**: finish the current level before going deeper.
-   **DFS = Deep first**: keep following a branch until you must
    backtrack.

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

```python
nums = [2,5,7,2,4,9,1,9,1]
freq = {}

'''
for i in nums:
  if i in freq: freq[i] += 1
  else: freq[i] = 1
'''
for i in nums:
    freq[i] = freq.get(i, 0) + 1  # dictionary.get(key, default_value)

print(freq)        # {2: 2, 5: 1, 7: 1, 4: 1, 9: 2, 1: 2}
print(len(freq))   # 6
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

## Linked List

A linked list is a linear data structure where elements are not stored in contiguous memory locations. Instead, each element is an independent object called a Node. Every node contains two parts: the data (the value being stored) and a pointer/reference to the next node in the sequence.

```python
class Node:
    """A single node in a linked list."""
    def __init__(self, data):
        self.data = data  # Stores the value
        self.next = None  # Points to the next node (initially None)


class LinkedList:
    """The linked list wrapper class."""
    def __init__(self):
        self.head = None  # The start of the list is initially empty

    def append(self, data):
        """Adds a new node to the end of the list."""
        new_node = Node(data)
        
        # If the list is empty, make the new node the head
        if not self.head:
            self.head = new_node
            return
            
        # Otherwise, traverse to the last node
        current = self.head
        while current.next:
            current = current.next
            
        # Link the last node to the new node
        current.next = new_node

    def display(self):
        """Prints the entire linked list."""
        current = self.head
        elements = []
        
        while current:
            elements.append(str(current.data))
            current = current.next
            
        print(" -> ".join(elements) + " -> None")


# --- How to use it ---
if __name__ == "__main__":
    # Create a new Linked List
    my_list = LinkedList()

    # Append data
    my_list.append(10)
    my_list.append(20)
    my_list.append(30)

    # Display the list
    # Output: 10 -> 20 -> 30 -> None
    my_list.display()
```

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

## Trie / Prefix tree

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

---

# Python vs Go at a Glance

```text
                    Python                          Go
Typing              dynamic, strong                 static, strong, inferred
Runs as             bytecode on the Python VM       native executable
Entry point         if __name__ == "__main__"       package main + func main()
Null value          None                            nil (pointers, slices, maps, interfaces...)
Unset variable      NameError                       zero value (0, "", false, nil)
Collections         list, tuple, dict, set          array, slice, map (set = map[T]struct{})
Strings             Unicode text, immutable         UTF-8 bytes, immutable, rune = character
Errors              exceptions: try/except/finally  error values: if err != nil, defer, panic/recover
Cleanup             with statement                  defer
Memory              reference counting + cyclic GC  concurrent tracing GC, pointers (no arithmetic)
Objects             classes, inheritance            structs, methods, interfaces, embedding
Interfaces          duck typing (runtime)           implicit, checked at compile time
Concurrency         threads (GIL), processes,       goroutines, channels, mutexes, context
                    asyncio
Parallel CPU work   multiprocessing                 goroutines on all cores
Dependencies        pip + venv + requirements /     go.mod + go.sum (minimal version selection)
                    pyproject.toml
Tests               pytest / unittest               go test (built in)
Format              black / ruff format             gofmt (one standard style)
```