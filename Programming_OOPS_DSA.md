# Programming Complete Guide (Python and TypeScript)

A single-file reference covering how programs are written, run, and organized, the concepts every language shares, built-ins, coding patterns, tricks, DSA, and OOP — each with a working code example. Python is the main language; TypeScript examples sit next to it wherever the topic matters. The DSA and OOP sections stay Python only.

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

## Installing TypeScript

TypeScript is a language that compiles to JavaScript, so you first need a JavaScript runtime. Install Node.js (pick the LTS version) from https://nodejs.org/; it includes `npm`, the package manager. TypeScript itself is then installed per project as a normal dependency.

```text
Windows : run the Node.js installer (it adds node and npm to PATH)
macOS   : nodejs.org installer, or:  brew install node
Linux   : use a version manager (recommended) or the package manager
          nvm install --lts                                     (nvm, fnm, or volta switch versions per project)
          sudo apt install nodejs npm                           (Debian/Ubuntu, often an older version)
```

```bash
node --version                      # the runtime
npm --version                       # the package manager

mkdir hello && cd hello
npm init -y                         # creates package.json
npm install --save-dev typescript @types/node
npx tsc --version                   # the compiler installed inside this project
npx tsc --init                      # creates tsconfig.json (compiler settings)
```

A minimal `tsconfig.json` for a Node.js project (the generated file has many more commented options):

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "NodeNext",
    "moduleResolution": "NodeNext",
    "outDir": "dist",
    "rootDir": "src",
    "strict": true,
    "noUncheckedIndexedAccess": true,
    "skipLibCheck": true
  },
  "include": ["src"]
}
```

Keep `strict` on: it turns on `strictNullChecks`, `noImplicitAny`, and the other checks that make TypeScript worth using. As with the system Python, do not rely on the Node.js that an operating system tool installed; use `nvm`, `fnm`, or `volta` so each project can pin its own Node version (a `.nvmrc` file or the `engines` field in `package.json`).

## Where to Write Code

Python and TypeScript source code is plain text. Any editor can write it; an IDE or a good editor adds autocomplete, error highlighting, a debugger, and test runners.

* Python files end in `.py`, TypeScript files end in `.ts` (`.tsx` when they contain JSX for React)
* Save the file, then run it from a terminal (next sections)
* A real application is a folder of files, not one file: an entry point, modules/packages, tests, and dependency files

Where the code eventually runs depends on the application:

* A script run by hand or by a scheduler (cron, CI pipeline)
* A command-line tool (`argparse` in Python, `commander` or `yargs` in TypeScript; Node also has a built-in `util.parseArgs`)
* A web service (Flask/FastAPI/Django behind gunicorn/uvicorn in Python, Express/Fastify/NestJS on Node.js in TypeScript) inside a container or on a server
* A browser application (React, Angular, Vue, Svelte), where TypeScript is compiled or bundled to JavaScript that the browser runs
* A notebook (Jupyter) for data work and experiments
* A serverless function (AWS Lambda, Cloud Functions)

## Choosing an IDE or Editor

* VS Code — free, works for both languages (TypeScript support is built in; install the Python extension for Python); the best all-round choice to start with
* PyCharm (Community is free) — full Python IDE, strongest for large Python projects, refactoring, and debugging
* WebStorm — the matching full IDE for TypeScript and JavaScript
* Jupyter / JupyterLab / Google Colab — notebooks for data analysis and learning, not for building applications
* IDLE — ships with Python, fine for the first day only
* Vim/Neovim/Emacs — powerful, but need plugins (language servers) to match an IDE

Whatever you pick, set it to use the interpreter of the project's virtual environment (Python) and the project's own `typescript` version from `node_modules` (TypeScript), so autocomplete and the debugger see the same libraries and compiler your code will run with.

## Your First Program

```python
# file: hello.py
def main():
    name = input("Your name: ")
    print(f"Hello, {name}!")

if __name__ == "__main__":
    main()
```

```ts
// file: src/hello.ts
import * as readline from "node:readline/promises";   // built-in Node.js module for reading input

async function main(): Promise<void> {                  // async because reading input waits
  const rl = readline.createInterface({ input: process.stdin, output: process.stdout });
  const name = await rl.question("Your name: ");
  console.log(`Hello, ${name}!`);
  rl.close();
}

main();                                                  // TypeScript has no special main(): the file simply runs top to bottom
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

## Running a TypeScript Program

```bash
npx tsc                          # type-check and compile src/*.ts into dist/*.js (as set in tsconfig.json)
node dist/hello.js               # run the compiled JavaScript
node dist/hello.js arg1 arg2     # arguments arrive in process.argv (index 0 = node, 1 = the script, 2+ = yours)

npx tsc --watch                  # recompile on every save
npx tsc --noEmit                 # only type-check, write no files (what CI usually runs)

npx tsx src/hello.ts             # run a .ts file directly (the tsx tool strips types and runs it; no type-check)
node src/hello.ts                # recent Node versions (22.18+ / 23.6+) can run .ts directly by stripping types
                                 # (only "erasable" syntax: no enums or namespaces; no type-check either)
node                             # start the REPL; leave with .exit or Ctrl+D
node -e "console.log('hi')"      # run one line
```

```json
// package.json: scripts give every project the same short commands
{
  "scripts": {
    "build": "tsc",
    "start": "node dist/hello.js",
    "dev": "tsx watch src/hello.ts",
    "typecheck": "tsc --noEmit",
    "test": "vitest run"
  }
}
```

```bash
npm run build
npm start
npm run typecheck
```

The exit code works the same way: `0` means success, anything else means failure (`process.exit(1)` or `process.exitCode = 1`). Remember: `tsc` checks types and translates; Node.js never sees your types. Runners such as `tsx` skip the type-check for speed, so keep a separate `tsc --noEmit` step.

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

myapp/                         (TypeScript)
├── node_modules/              # installed dependencies, never committed
├── package.json               # project info + dependencies + scripts
├── package-lock.json          # exact versions of everything installed
├── tsconfig.json              # compiler settings
├── src/
│   ├── index.ts               # entry point: reads arguments, calls the logic
│   ├── services.ts            # the actual logic, importable and testable
│   └── services.test.ts
└── dist/                      # compiled JavaScript output, never committed
```

Keep the entry point thin: read configuration, wire things together, call into modules that can be imported and tested without starting the whole program.

## How Source Code Becomes a Running Program

Source code is text a human writes. A CPU can only run machine code, so something must translate it. There are two classic strategies:

* Compiled: a compiler translates the whole program ahead of time into machine code. You run the resulting file. (C, Rust)
* Interpreted: an interpreter reads the program and executes it while running. (the original idea behind Python, Ruby, JavaScript)

In practice the line is blurry, and it belongs to the implementation, not the language. CPython (the standard Python) first compiles source to bytecode and then a virtual machine interprets that bytecode. TypeScript is a two-step case: the TypeScript compiler (`tsc`, or faster tools such as esbuild and swc) checks the types and translates `.ts` into plain JavaScript, erasing every type annotation; then a JavaScript engine (V8 in Node.js and Chrome) parses that JavaScript into bytecode and uses a JIT compiler to turn hot code into machine code while the program runs. Other runtimes (the JVM, PyPy) also add a JIT.

```text
Python:      hello.py  --compile-->  bytecode (.pyc)  --Python VM executes-->  CPU
TypeScript:  hello.ts  --tsc: check types, erase them-->  hello.js
                       --V8: bytecode, then JIT-->  machine code  -->  CPU
```

### Source Code, Bytecode, Machine Code, Executable

| Term | What it is | In Python | In TypeScript |
| :--- | :--- | :--- | :--- |
| Source code | Human-readable text | `hello.py` | `hello.ts` |
| Intermediate output | What the compiler step produces | `__pycache__/hello.cpython-3xx.pyc` (bytecode cache) | `dist/hello.js` (plus optional `.d.ts` type files and `.js.map` source maps) |
| Bytecode | Compact instructions for a virtual machine, same on every OS/CPU | the `.pyc` file | lives only inside V8 while the program runs; not a file you ship |
| Machine code | Instructions for one specific CPU (x86-64, ARM64) | only inside the interpreter itself | generated by V8's JIT at run time, never saved to disk |
| Executable | A file the OS can launch: machine code + metadata (`.exe`, ELF, Mach-O) | `python` is the executable; your `.py` is just its input | `node` is the executable; your compiled `.js` is just its input |

Python compiles each imported module to bytecode and caches it in `__pycache__`, so the next start is faster. You can look at the bytecode with the `dis` module:

```python
import dis

def add(a, b):
    return a + b

dis.dis(add)
# Prints instructions such as: LOAD_FAST a, LOAD_FAST b, BINARY_OP (+), RETURN_VALUE
# (the exact instructions change between Python versions)
```

TypeScript's compile step is mostly "erase the types". Compare the input and the output:

```ts
// add.ts (input)
function add(a: number, b: number): number {
  return a + b;
}
```

```js
// add.js (output of tsc): the annotations are gone, nothing about "number" exists at run time
function add(a, b) {
    return a + b;
}
```

Because types vanish, a TypeScript program cannot check a value's TypeScript type while it runs; it can only test JavaScript facts such as `typeof x === "string"`. (V8 bytecode can be printed with `node --print-bytecode`, but you almost never need it.)

A Python program needs a Python installation plus its libraries on the target machine (or a container image, or a bundler such as PyInstaller). A TypeScript program needs Node.js (or Deno or Bun) plus `node_modules` on the target machine, or a container image, or a bundler (esbuild, webpack, Vite) that packs everything into one file. Single-file executables also exist (`node --experimental-sea-config`, `bun build --compile`, `deno compile`).

### What the Runtime Does

The runtime is the code that runs alongside your program and does the work the language promises. Your code never calls most of it directly.

* Memory management: allocating objects, tracking who still uses them, garbage collection
* Executing code: the Python VM loop; in Node.js the V8 engine (interpreter plus JIT compiler)
* Scheduling: Python threads (under the GIL); in Node.js the event loop, which runs your JavaScript on one thread and hands waiting work (network, disk, timers) to the operating system and helper threads
* Safety checks: type checks and exceptions in Python; in TypeScript the types are already erased, so the runtime only throws errors such as `TypeError` for things like reading a property of `undefined`
* Built-in data structures: dict/list in Python, Object/Map/Array in TypeScript
* Importing and loading code: Python resolves imports at run time; Node.js loads modules (ES modules or CommonJS) at run time too, while `tsc` only checks at compile time that the import path exists

In Python the runtime is the interpreter. In TypeScript the runtime is the JavaScript engine plus the host environment (Node.js, Deno, Bun, or a browser), which supplies things like `fs`, `fetch`, and timers.

## Kinds of Errors

### Syntax Errors

The code is not valid for the language. Python reports them when it compiles the file (before any line runs). TypeScript reports them at compile time too, and it also reports type errors in the same pass.

```python
# print("hello"        -> SyntaxError: '(' was never closed
# if x = 5:            -> SyntaxError: invalid syntax (use == for comparison)
```

```ts
// console.log("hello"                 -> error TS1005: ')' expected
// let x: number = ;                   -> error TS1109: Expression expected
// const n: number = "hi";             -> error TS2322: Type 'string' is not assignable to type 'number'
// let count = 1; count = "a";         -> error TS2322 (the type was inferred as number)
// const unused = 5;                   -> error TS6133 only when "noUnusedLocals" is turned on
```

Note that by default `tsc` still writes the `.js` files even when it found errors (unless you set `noEmitOnError`), so a build can "succeed" with errors; make CI fail on a non-zero exit code from `tsc`.

### Runtime Errors

The code is valid, but something goes wrong while it runs: bad input, missing file, network down, bug in a path nobody tested.

```python
nums = [1, 2, 3]
# nums[10]        -> IndexError
# int("abc")      -> ValueError
# 10 / 0          -> ZeroDivisionError
# None.upper()    -> AttributeError
```

```ts
const nums = [1, 2, 3];
// nums[10]                     -> undefined (NO error; with noUncheckedIndexedAccess the type becomes number | undefined)
// JSON.parse("abc")            -> SyntaxError: Unexpected token 'a', "abc" is not valid JSON
// 10 / 0                       -> Infinity (NO error)
// Number("abc")                -> NaN (NO error)
// 1n + 1                       -> TypeError: Cannot mix BigInt and other types

const user = JSON.parse('{"name": null}') as { name: string };   // the compiler believes the cast, the data disagrees
// user.name.toUpperCase()      -> TypeError: Cannot read properties of null (reading 'toUpperCase')
```

Python raises an exception almost every time something is off. JavaScript and TypeScript often keep going with `undefined`, `NaN`, or `Infinity`, and the failure shows up later and further away. See Exception Handling.

### Logical Errors

The program runs without any error and gives the wrong answer. No tool can report them for you. You find them with tests, print/log statements, and a debugger.

```python
def average(nums):
    return sum(nums) / len(nums) + 1     # bug: no error, wrong result

print(average([2, 4, 6]))                # 5.0, expected 4.0
```

```ts
function average(nums: number[]): number {
  return nums.reduce((a, b) => a + b, 0) / nums.length + 1;   // bug: compiles, runs, wrong answer
}
console.log(average([2, 4, 6]));                                // 5, expected 4
```

Static typing (TypeScript) turns whole categories of runtime errors into compile errors. Python can get part of that with type hints and a checker such as `mypy`.

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

A Node.js error prints the failing source line, then the error type and message, then the stack with the most recent call first, so you read it from the top down:

```text
/home/me/app/dist/app.js:3
    throw new Error(`invalid age: ${text}`);
          ^

Error: invalid age: abc
    at parseAge (/home/me/app/dist/app.js:3:11)
    at main (/home/me/app/dist/app.js:6:12)
    at Object.<anonymous> (/home/me/app/dist/app.js:9:1)
```

The line numbers refer to the compiled `.js` file. Run Node with `--enable-source-maps` (and keep `"sourceMap": true` in `tsconfig.json`) and the stack shows the original `.ts` lines instead.

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

## Variables and Data Types in TypeScript

TypeScript is statically typed: every variable has a type that the compiler knows before the program runs. Most of the time you do not write the type, because the compiler infers it from the value. The types exist only at compile time and are erased from the JavaScript that runs.

```ts
const name: string = "Alice";        // string (annotation written out)
let age = 25;                         // number (type inferred)
let height = 5.6;                     // number (every number is a 64-bit float)
const isActive = true;                // boolean
let data: string | null = null;       // a union type: either a string or null
let missing: number | undefined;      // declared but not assigned: its value is undefined

console.log(typeof age);              // "number"  (typeof is a run-time check on the JavaScript value)
console.log(data === null);           // true
```

### let vs const vs var

```ts
let x = 1;               // can be reassigned; block scoped
const y = 10;            // cannot be reassigned; block scoped (prefer this by default)
var z = 3;               // old style: function scoped and hoisted; avoid it

const items = [1, 2];
items.push(3);           // allowed: const fixes the binding, not the contents of the array
// items = [];           // compile error: Cannot assign to 'items' because it is a constant

let a = 1, b = "two";                                  // several at once
const { host, port } = { host: "localhost", port: 8080 };   // destructuring an object
const [first, second] = [10, 20];                      // destructuring an array

let n: number;
// console.log(n);       // compile error: Variable 'n' is used before being assigned
```

* Use `const` unless the variable really is reassigned, then use `let`; never use `var`
* `const` protects the name, not the value; use `readonly`, `as const`, or `Object.freeze` for immutable data
* An unused variable is not an error by default; turn on `noUnusedLocals` and `noUnusedParameters` in `tsconfig.json` (or use ESLint) to catch them

### Default Values: undefined, Not Zero

TypeScript has no zero values. A variable that is declared without a value is `undefined`, and strict mode refuses to let you read it before it is assigned. If you want a default, write it down.

```ts
let count: number | undefined;           // undefined until assigned
const total = count ?? 0;                // ?? gives a default only for null/undefined
function greet(name = "world") {         // default parameter value
  return `Hello, ${name}`;
}

class Point {
  x = 0;                                 // class fields get their defaults from initializers
  y = 0;
  label = "";
}
const pt = new Point();                  // Point { x: 0, y: 0, label: '' }

const empty: number[] = [];              // an empty array
const sparse = new Array<number>(3);     // [ <3 empty items> ]: holes, not zeros (use .fill(0))
const zeros = new Array<number>(3).fill(0);   // [0, 0, 0]
```

* `undefined` means "no value was ever given"; `null` means "deliberately empty"; with `strictNullChecks` (part of `strict`) the compiler makes you handle both before use
* An array declared with `[]` is empty and usable; an object property that was never set reads as `undefined`
* Python has the same split: a name that was never assigned does not exist (`NameError`), and "no value" is written explicitly as `None`

### Constants and Enums

```ts
const PI = 3.14159;

const enum Direction { Up, Down }                 // see the Enums section for the full story
enum Day { Sunday, Monday, Tuesday }              // Sunday = 0, Monday = 1, Tuesday = 2

const CONFIG = { retries: 3, mode: "fast" } as const;   // readonly, with literal types
// CONFIG.retries = 5;                                  // compile error: it is a read-only property
```

Python has no real constants; the convention is an UPPER_CASE name (`MAX_RETRIES = 3`) that nobody should reassign.

### Basic Types

```ts
boolean
string
number                      // 64-bit float; whole numbers are exact up to 2^53 - 1 (Number.MAX_SAFE_INTEGER)
bigint                      // arbitrary-size integers: 123n
symbol                      // unique identifiers
null
undefined
void                        // "returns nothing" (a function result)
never                       // a value that cannot exist (a function that always throws)
unknown                     // "could be anything": must be checked before use (safe)
any                         // switches the type checker off for that value (avoid)
string[]  /  Array<string>  // arrays
[string, number]            // tuple: fixed length and position types
{ id: number; name: string }  // object type
```

There is no separate `int`, `float`, `byte`, or `char`: all numbers are `number` (or `bigint`), and a "character" is just a string of length 1.

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

## Type Conversion in TypeScript

TypeScript has no conversion functions of its own: you use JavaScript's, and they are forgiving. Converting text to a number does not throw when the text is bad, it quietly gives `NaN` ("not a number"), so you must check the result. Also remember that a type assertion (`x as number`) is not a conversion; it only tells the compiler to trust you.

```ts
// number <-> string
const s = String(42);                    // "42"   (also `${42}` or (42).toString())
const n = Number("42");                  // 42
Number("3.14");                          // 3.14
Number("abc");                           // NaN    no exception!
Number("");                              // 0      surprise: an empty string becomes 0
Number(null);                            // 0
Number(undefined);                       // NaN
Number.isNaN(Number("abc"));             // true   the safe way to test (NaN === NaN is false)

parseInt("42px", 10);                    // 42     stops at the first invalid character
parseInt("ff", 16);                      // 255    (string, base)
parseFloat("3.14abc");                   // 3.14
(255).toString(16);                      // "ff"
(10).toString(2);                        // "1010"
(3.14159).toFixed(2);                    // "3.14" (returns a STRING)

// float -> int
Math.trunc(3.99);                        // 3    truncates toward zero, like Python int()
Math.round(3.5);                         // 4
Math.floor(-3.5);                        // -4
Math.ceil(3.2);                          // 4

// boolean
Boolean(0);                              // false
Boolean("");                             // false
Boolean("false");                        // true  non-empty string is truthy, even "false"!
Boolean("0");                            // true
Boolean([]);                             // true  an empty array is truthy (Python: falsy)
!!"text";                                // true  double negation: a common shortcut

// bigint
const big = BigInt(10);                  // 10n
Number(10n);                             // 10

// string <-> array <-> characters
Array.from("abc");                       // ["a", "b", "c"]   (also [..."abc"], splits by code point)
"dog cat".split(" ");                    // ["dog", "cat"]
["P", "y", "t"].join("");                // "Pyt"
"A".charCodeAt(0);                       // 65
"😀".codePointAt(0);                     // 128512
String.fromCharCode(65);                 // "A"
String.fromCodePoint(128512);            // "😀"

// objects <-> JSON
const json = JSON.stringify({ name: "Alice", age: 25 });   // object -> JSON string
const back = JSON.parse(json);                              // JSON string -> any (validate it!)

// collections
const set = new Set([1, 2, 2, 3]);       // Set {1, 2, 3}
const arr = [...set];                    // [1, 2, 3]    (also Array.from(set))
const entries = Object.entries({ a: 1, b: 2 });   // [["a", 1], ["b", 2]]
const obj = Object.fromEntries(entries);          // { a: 1, b: 2 }
const map = new Map(entries);                     // Map(2) {"a" => 1, "b" => 2}
```

### Mixing Types: What the Compiler Stops

```ts
const a: number = 1;
const b: bigint = 2n;
// const c = a + b;           // compile error: Operator '+' cannot be applied to types 'number' and 'bigint'
const c = BigInt(a) + b;      // convert one side explicitly

// "5" - 2                    // compile error in TypeScript (the answer is 3 in plain JavaScript)
"5" + 2;                      // "52"  allowed: + with a string is always concatenation
Number("5") - 2;              // 3     say what you mean
```

The compiler blocks the worst coercions (arithmetic on strings, mixing `bigint` and `number`) but still allows `+` with a string. It also cannot protect you from data it never saw, such as the result of `JSON.parse`.

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

## Operators in TypeScript

```ts
7 / 2                 // 3.5   always float division: there is no integer division operator
Math.trunc(7 / 2)     // 3     truncate toward zero
Math.floor(7 / 2)     // 3     Math.floor(-7 / 2) is -4, the same as Python's //
-7 % 3                // -1    % takes the sign of the dividend; Python gives 2 (the sign of the divisor)
((-7 % 3) + 3) % 3    // 2     the usual trick for a Python-style (always non-negative) modulo
2 ** 10               // 1024  exponent operator, like Python's **

// Bitwise operators work on 32-bit SIGNED integers (the number is converted first)
const a = 5, b = 3;
a & b                 // 1     AND
a | b                 // 7     OR
a ^ b                 // 6     XOR
~a                    // -6    NOT
a << 1                // 10
a >> 1                // 2     sign-preserving right shift
-5 >>> 1              // 2147483645   unsigned right shift (fills with zeros)
1 << 31               // -2147483648  it wraps into the sign bit
2 ** 32 | 0           // 0     anything above 32 bits is cut off (use BigInt for big masks)

let x = 5;
x++;                  // increment works as a statement or an expression
x += 2;

// Logical: && || ! (not "and", "or", "not")
const ok = x > 3 && x < 10;
const name = "" || "default";       // || falls back on ANY falsy value: "" -> "default"
const count = 0 ?? 5;               // ?? falls back only on null/undefined: 0 stays 0
user?.profile?.email;               // ?. optional chaining: undefined instead of an error
let cache: string | undefined;
cache ??= "loaded";                 // assign only if null/undefined (also ||= and &&=)

// Ternary and comparison
const label = x >= 18 ? "adult" : "minor";    // the conditional operator (Python: a if c else b)
1 === 1;                            // strict equality: no conversion (use this)
1 == "1";                           // true: loose equality converts types (avoid it)
```

* There is no walrus operator, but assignment is an expression, so `while ((m = re.exec(text)) !== null)` works
* Prefer `===` and `!==` over `==` and `!=`; the loose forms convert types in surprising ways

---

# Type Systems

## Static vs Dynamic Typing

The type system decides when a value's type is checked.

* Dynamic typing (Python): types belong to values, not to names. A name can be rebound to any type, and type mistakes are found only when the offending line actually runs.
* Static typing (TypeScript): types belong to variables and expressions, are fixed, and are checked by the compiler before the program ever runs. (The checked types are then erased, and the JavaScript that runs is dynamically typed.)

```python
x = 10
x = "ten"            # allowed: the name x now points to a str

def add(a, b):
    return a + b

add(1, 2)            # 3
add("a", "b")        # "ab"   same function, different types (duck typing)
add(1, "b")          # TypeError, discovered only when this line executes
```

```ts
let x: number = 10;
// x = "ten";          // compile error: Type 'string' is not assignable to type 'number'

function add(a: number, b: number): number { return a + b; }
// add("a", "b");      // compile error: Argument of type 'string' is not assignable to parameter of type 'number'

```

Trade-off: dynamic typing is quick to write and flexible; static typing finds a whole class of bugs before shipping, makes refactoring safer, and lets tools autocomplete reliably, at the cost of more up-front declarations. TypeScript is called gradually typed: the escape hatches `any` and `as` let you opt out locally, which is useful when migrating JavaScript and dangerous when overused.

## Type Inference

Inference means the compiler works out a type from the value, so you do not write it. It is not the same as dynamic typing: the type is still fixed at compile time.

```ts
const count = 10;             // type: 10 (a literal type, because const cannot change)
let total = 10;               // type: number (let can change, so it is widened)
let price = 9.99;             // number
const name = "TS";            // type: "TS"
let ok = true;                // boolean
const items: string[] = [];   // needs the annotation; a bare [] would be too vague
const ch = "a";               // string: there is no char type
const nums = [1, 2, 3];       // number[]
const pair = [1, "a"];        // (string | number)[]   (use "as const" or a tuple type to keep positions)
```

Inference stops at function boundaries: parameters must be annotated (except callbacks whose type comes from context, such as the argument of `.map`). The return type is inferred, but writing it on exported functions documents the contract and catches mistakes.

Python never declares types at all. Type hints are optional documentation plus input for checkers such as `mypy` or `pyright`; Python itself ignores them at runtime:

```python
def double(n: int) -> int:
    return n * 2

double("ab")        # runs fine and returns "abab"; mypy would flag it before you run it
```

TypeScript's annotations are different in one way: the compiler enforces them, and a type error stops a strict build. In another way they are the same: like Python's hints, they are erased and never checked while the program runs.

## Structural vs Nominal Typing

TypeScript compares types by their shape, not by their name. If a value has the properties a type requires, it fits, no matter what it was declared as. This is the compile-time version of Python's duck typing.

```ts
interface Point { x: number; y: number }

const p = { x: 1, y: 2, label: "origin" };
const q: Point = p;                         // OK: p has x and y, which is all Point asks for

function norm(pt: Point): number { return Math.hypot(pt.x, pt.y); }
norm({ x: 3, y: 4 });                       // 5
// norm({ x: 3, y: 4, label: "a" });        // compile error: object literals are checked for extra properties
// norm({ x: 3 });                          // compile error: Property 'y' is missing
```

```python
class Duck:
    def quack(self): return "Quack"
class Robot:
    def quack(self): return "Beep"
for thing in [Duck(), Robot()]:
    print(thing.quack())            # Python checks nothing until this line runs
```

## Strong vs Weak Typing

This is a different question from static vs dynamic: how willing is the language to silently convert between unrelated types?

* Strong: mixing incompatible types is an error. Python is strong but dynamic.
* Weak: the language converts silently and keeps going, which hides bugs. JavaScript, the language TypeScript compiles to, is weak.

```python
"1" + 1            # TypeError: can only concatenate str (not "int") to str
"1" + str(1)       # "11"   you must say what you mean
```

```javascript
// plain JavaScript (weakly typed), shown for contrast
"1" + 1            // "11"   number silently turned into a string
"5" - 2            // 3      string silently turned into a number
[] + {}            // "[object Object]"
```

```ts
// TypeScript's checker rejects the worst of these before the code runs
// "5" - 2          // error TS2362: The left-hand side of an arithmetic operation must be of type 'any', 'number', 'bigint' or an enum type
"1" + 1;            // still allowed: "11"
```

|  | Dynamic | Static |
| :--- | :--- | :--- |
| Strong | Python | Rust, Java |
| Weak | JavaScript, PHP | C, and TypeScript's runtime (it is JavaScript); the checker adds static rules on top |

TypeScript sits between the boxes: the compiler is static and fairly strict, but it is deliberately not sound (`any`, type assertions, and unchecked array indexing can all lie), and the program that runs is JavaScript with all its coercions.

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

```ts
1 + "2";                       // "12"   number turned into a string
"3" * "4";                     // 12     JavaScript converts (TypeScript flags it as a compile error)
if ("") { }                    // empty string is falsy
if ([]) { /* runs */ }         // an empty array is truthy (Python: falsy)
console.log(`${42}`);          // template literals call String() for you

Number("5");                   // explicit
```

Falsy values in TypeScript/JavaScript: `false`, `0`, `-0`, `0n`, `""`, `null`, `undefined`, `NaN`. Everything else is truthy, including `[]`, `{}`, and `"0"`. Prefer explicit conversions and explicit checks (`arr.length === 0`, `value === undefined`): the reader sees exactly where a value changes type and where data may be lost (for example `float → int` truncation, or `NaN`).

## None, undefined, and null

Each language needs a way to say "no value here". The name and the exact meaning differ:

| Language | Name | What it is |
| :--- | :--- | :--- |
| Python | `None` | A single object of type `NoneType`; compare with `is None` |
| TypeScript / JavaScript | `undefined` and `null` | Two separate values: `undefined` is "nothing was assigned or the property is missing", `null` is "deliberately empty"; with `strictNullChecks` the compiler tracks both |
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

```ts
let result: string | null = null;
if (result === null) {                          // use ===, not ==
  console.log("nothing yet");
}

function noReturn(): void {}
console.log(noReturn());                        // undefined: functions without return give back undefined

// Classic bug
const user: { name: string } | undefined = undefined as { name: string } | undefined;
// user.name                                    // compile error: 'user' is possibly 'undefined' (a TypeError if it slipped through)
user?.name;                                     // undefined: optional chaining stops at the first null/undefined
user?.name ?? "anonymous";                      // "anonymous"

// Famous gotchas
typeof null;                                    // "object"   a historical bug in the language
null == undefined;                              // true       loose equality treats them as equal
null === undefined;                             // false
0 || 5;                                         // 5          || replaces every falsy value
0 ?? 5;                                         // 0          ?? replaces only null and undefined
```

Always decide what "missing" means for each value: return `T | undefined` or `T | null` (or a result object) in TypeScript, return `None` or raise an exception in Python, and check before you use it.

## Type Errors

A type error means an operation was applied to a value of the wrong type.

* Python reports it at run time as `TypeError` (or `AttributeError`, `ValueError`) on the line that executes
* TypeScript reports almost all of them at compile time; the ones left come from `any`, type assertions (`as`), and data that came from outside (`JSON.parse`, network responses, user input) that did not match its declared type

```python
len(5)                    # TypeError: object of type 'int' has no len()
"abc" * "d"               # TypeError: can't multiply sequence by non-int of type 'str'
int([1, 2])               # TypeError: int() argument must be a string, a bytes-like object or a real number
```

```ts
const v: unknown = "hello";                 // unknown: must be checked before use
// v.toUpperCase();                         // compile error: 'v' is of type 'unknown'
if (typeof v === "string") {
  v.toUpperCase();                          // fine: narrowed to string inside the if
}

const n = v as number;                      // compiles! an assertion is a promise, not a check
// n.toFixed(2);                            // TypeError at run time: n.toFixed is not a function

const data = JSON.parse('{"age": "twelve"}') as { age: number };   // the compiler believes you
```

Habit that prevents most type errors: turn on `strict`, avoid `any` (use `unknown` and narrow it), and validate data at the program's edges (user input, files, APIs) with a schema library such as Zod or Valibot. In Python the same habit is: add type hints, run `mypy` or `pyright` in CI, and validate data at the edges.

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

## if and switch in TypeScript

```ts
const age = 20;

if (age < 13) {                      // parentheses are required; braces are optional but recommended
  console.log("child");
} else if (age < 18) {
  console.log("teen");
} else {
  console.log("adult");
}

// Truthiness exists (falsy: false, 0, "", null, undefined, NaN), but an empty array is TRUTHY
const items: number[] = [];
if (items.length === 0) {            // check emptiness explicitly
  console.log("empty");
}

// Ternary: the TypeScript version of Python's "a if c else b"
const label = age >= 18 ? "adult" : "minor";

// switch: a case FALLS THROUGH into the next one unless you break (the opposite of Python's match)
const day = "sat";
switch (day) {
  case "sat":
  case "sun":                        // two labels sharing one body (grouping by falling through)
    console.log("weekend");
    break;
  case "fri":
    console.log("almost");
    break;                           // forget this and the code runs on into the next case
  default:
    console.log("weekday");
}

// switch (true): a tidy if/else chain
switch (true) {
  case age < 13: console.log("child"); break;
  case age < 18: console.log("teen"); break;
  default: console.log("adult");
}
```

Narrowing is TypeScript's version of pattern matching: after a check, the compiler knows the narrower type. With a discriminated union (every variant has a literal `kind` field) a `switch` can be checked for completeness:

```ts
type Shape =
  | { kind: "circle"; radius: number }
  | { kind: "rect"; width: number; height: number };

function area(s: Shape): number {
  switch (s.kind) {
    case "circle": return Math.PI * s.radius ** 2;      // s is the circle variant here
    case "rect":   return s.width * s.height;           // s is the rect variant here
    default: {
      const _exhaustive: never = s;                     // compile error if a new variant is added and not handled
      return _exhaustive;
    }
  }
}
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

## Loops in TypeScript

TypeScript has the C-style `for`, `while`, and `do...while`, plus `for...of` for iterating values, which is the one you will use most.

```ts
// classic three-part loop
for (let i = 0; i < 5; i++) {
  console.log(i);                  // 0 1 2 3 4
}

// while loop and do...while (the body runs at least once)
let n = 5;
while (n > 0) {
  n--;
}
do { n++; } while (n < 3);

// infinite loop with controlled exit
let count = 0;
while (true) {
  count++;
  if (count > 3) break;
}

// there is no range(): build the numbers
Array.from({ length: 5 }, (_, i) => i);        // [0, 1, 2, 3, 4]
Array.from({ length: 3 }, (_, i) => i + 2);    // [2, 3, 4]  (range(2, 5))
[...Array(5).keys()];                          // [0, 1, 2, 3, 4]

// for...of: iterate VALUES of any iterable (array, string, Map, Set, generator)
const fruits = ["apple", "banana", "cherry"];
for (const fruit of fruits) {
  console.log(fruit);
}
for (const [index, fruit] of fruits.entries()) {     // enumerate
  console.log(index, fruit);
}
for (const ch of "héllo") {}                         // iterates code points

// for...in: iterates property NAMES (keys) of an object; avoid it on arrays
const d = { a: 1, b: 2 };
for (const key in d) {
  console.log(key);                                  // "a", "b"
}
for (const [key, value] of Object.entries(d)) {      // the better way to loop over an object
  console.log(key, value);
}

// Map and Set
const m = new Map([["a", 1], ["b", 2]]);
for (const [key, value] of m) {
  console.log(key, value);
}

// zip: no built-in; use the index
const names = ["Alice", "Bob"];
const scores = [90, 85];
names.forEach((name, i) => console.log(name, scores[i]));   // Alice 90, Bob 85

// continue and break, with a label to leave an outer loop
outer:
for (let i = 0; i < 3; i++) {
  for (let j = 0; j < 3; j++) {
    if (j === 1) continue outer;       // next iteration of the OUTER loop
    if (i === 2) break outer;          // leave both loops
    console.log(i, j);
  }
}
```

* TypeScript has no loop `else`, no `pass` (an empty `{}` is fine), and no comprehensions; use `map`, `filter`, `reduce`, `flatMap` instead
* `forEach` cannot be stopped with `break` or `return`, and it does not wait for `async` callbacks; use `for...of` when you need either
* `for (const x of arr)` hands you a copy of the value for primitives, but objects are references, so changing `x.field` changes the object in the array
* `let` in a `for` loop gives every iteration its own variable (this matters for closures; see Scope and Lifetime)

```ts
const squares = Array.from({ length: 5 }, (_, i) => i * i);          // list comprehension
const evens = [1, 2, 3, 4].filter((x) => x % 2 === 0);               // with a condition
const total = [1, 2, 3].reduce((acc, x) => acc + x, 0);              // sum(...)
const flat = [[1, 2], [3, 4]].flatMap((row) => row);                 // [1, 2, 3, 4]
const matrix = Array.from({ length: 3 }, (_, i) =>
  Array.from({ length: 3 }, (_, j) => i * j));                       // nested comprehension
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

## Strings in TypeScript

A TypeScript `string` is an immutable sequence of UTF-16 code units. `length` counts code units, not characters, so characters outside the basic range (most emoji) count as two. Spreading or iterating a string walks code points instead.

```ts
const s = "héllo";
s.length;                          // 5   code units (é is one unit here)
const emoji = "😀";
emoji.length;                      // 2   one character, two UTF-16 units
[...emoji].length;                 // 1   spreading iterates code points
s[1];                              // "é" indexing gives a one-character string (no char type)
// s[0] = "H";                     // compile error: Cannot assign to '0' because it is a read-only property
"H" + s.slice(1);                  // "Héllo"  build a new string

const bytes = new TextEncoder().encode(s);     // Uint8Array: UTF-8 bytes
bytes.length;                                  // 6
new TextDecoder("utf-8").decode(bytes);        // "héllo"

const multi = `line one
line two`;                         // template literal: multi-line, and ${...} interpolation
```

```ts
"  Hello World  ".trim();                  // "Hello World"   (trimStart / trimEnd too)
"ts".toUpperCase();                        // "TS"
"TS".toLowerCase();                        // "ts"
"a,b,c".split(",");                        // ["a", "b", "c"]
"dog  cat".split(/\s+/);                   // ["dog", "cat"]  (split on any whitespace, like Python split())
["a", "b"].join("-");                      // "a-b"
"hello".includes("ell");                   // true
"hello".startsWith("he");                  // true
"hello".endsWith("lo");                    // true
"hello".indexOf("l");                      // 2   (-1 if not found)
"banana".split("a").length - 1;            // 3   count occurrences
"a-b-c".replaceAll("-", "+");              // "a+b+c"
"ab".repeat(3);                            // "ababab"
"abc".padStart(5, "0");                    // "00abc"   (Python zfill)
"abc".at(-1);                              // "c"       negative index with .at()
```

Slicing uses `slice(start, end)`; `end` is excluded, and negative indexes count from the end, as in Python. There is no step argument, so reversing takes a few calls.

```ts
const t = "Programming";
t.slice(0, 6);                             // "Progra"
t.slice(-6, -1);                           // "rammin"
t.slice(4);                                // "ramming"
[...t].reverse().join("");                 // "gnimmargorP"  (Python: t[::-1])
[...t].filter((_, i) => i % 2 === 0).join("");   // "Pormig"  (Python: t[::2])
```

```ts
// Building a string in a loop: collect the pieces and join once
const parts: string[] = [];
for (let i = 0; i < 3; i++) {
  parts.push(`${i},`);
}
const result = parts.join("");             // "0,1,2,"
// (+= on strings is also fast in modern engines, but joining is the clearest habit for big loops)
```

Formatting uses template literals, the TypeScript version of f-strings, plus `toFixed`, `padStart`, and `Intl`:

```ts
const name = "Bob", age = 30, price = 3.14159;
console.log(`${name} is ${age} years old`);        // Bob is 30 years old
console.log(`Next year: ${age + 1}, ${name.toUpperCase()}`);   // expressions inside ${}
price.toFixed(2);                                  // "3.14"   (Python :.2f)
String(7).padStart(3, "0");                        // "007"    (Python :03)
(1000000).toLocaleString("en-US");                 // "1,000,000"   (Python :,)
(0.75).toLocaleString("en-US", { style: "percent" });   // "75%"
new Intl.NumberFormat("en-US", { style: "currency", currency: "USD" }).format(1234.5);   // "$1,234.50"
console.log("%s is %d", name, age);                // console.log understands %s %d %o placeholders
console.log({ name, age });                        // prints the object: { name: 'Bob', age: 30 }
console.log(JSON.stringify({ name, age }, null, 2));   // pretty-printed JSON
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

## Math in TypeScript

```ts
Math.abs(-5);                    // 5
Math.pow(2, 10);                 // 1024   (or 2 ** 10)
Math.sqrt(16);                   // 4
Math.cbrt(27);                   // 3
Math.ceil(4.1);                  // 5
Math.floor(4.9);                 // 4
Math.round(3.14159 * 100) / 100; // 3.14   there is no round(x, digits); scale, round, scale back (or toFixed)
Math.max(3, 7, 2);               // 7
Math.min(3, 7, 2);               // 2
Math.max(...[3, 7, 2]);          // spread an array into the arguments
[1, 2, 3].reduce((a, b) => a + b, 0);    // 6   (sum)
[Math.floor(9 / 2), 9 % 2];      // [4, 1]   (divmod)
Math.hypot(3, 4);                // 5
Math.PI; Math.E;
Math.random();                   // a float in [0, 1); not cryptographically secure (use crypto.randomUUID / getRandomValues)
Math.floor(Math.random() * 10) + 1;     // random integer 1..10 (Python randint(1, 10))

// gcd and lcm are not built in
const gcd = (a: number, b: number): number => (b === 0 ? a : gcd(b, a % b));
const lcm = (a: number, b: number): number => (a / gcd(a, b)) * b;
gcd(24, 36);                     // 12
lcm(12, 18);                     // 36

// checks (the string checks Python offers as methods are regular expressions here)
Number.isInteger(5.0);           // true
Number.isFinite(1 / 0);          // false
Number.isNaN(NaN);               // true
/^[a-z0-9]+$/i.test("abc123");   // true   isalnum
/^[a-z]+$/i.test("abc");         // true   isalpha
/^\d+$/.test("123");             // true   isdigit
/^\s+$/.test("   ");             // true   isspace
```

## Precision, Overflow and Rounding

Python integers have unlimited size, so they never overflow. Floats are IEEE 754 binary numbers, so many decimals cannot be stored exactly. TypeScript's `number` is always one of those 64-bit floats: there is no separate integer type, so whole numbers lose exactness past 2^53, and there is no wrap-around overflow. For true big integers there is `bigint`.

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

```ts
Number.MAX_SAFE_INTEGER;                  // 9007199254740991  (2^53 - 1): the largest integer that is always exact
9007199254740992 === 9007199254740993;    // true!  beyond that, neighbouring integers collapse into one value
Number.isSafeInteger(2 ** 53);            // false
2 ** 1024;                                // Infinity  (no wrap-around, it just overflows to Infinity)

const a = 0.1, b = 0.2;
a + b;                                    // 0.30000000000000004  same IEEE 754 behavior as Python
a + b === 0.3;                            // false
Math.abs(a + b - 0.3) < Number.EPSILON;   // true   compare floats with a tolerance
(0.1 + 0.2).toFixed(2);                   // "0.30"  a string for display, not a fix for the maths

10 / 0;                                   // Infinity   no exception (Python raises ZeroDivisionError)
-10 / 0;                                  // -Infinity
0 / 0;                                    // NaN
NaN === NaN;                              // false  NaN is never equal to anything, even itself
Number.isNaN(NaN);                        // true   (the global isNaN("abc") converts first: avoid it)
Object.is(NaN, NaN);                      // true

// huge numbers: bigint (suffix n); it cannot be mixed with number
2n ** 100n;                               // 1267650600228229401496703205376n
BigInt(Number.MAX_SAFE_INTEGER) + 2n;     // 9007199254740993n  exact
// 2n ** 100n + 1                         // compile error: cannot mix bigint and number
// Math.sqrt(4n)                          // compile error: Math works on number only

Math.round(2.5);                          // 3   rounds half toward +Infinity (Python rounds half to even)
Math.round(-2.5);                         // -2  (so negative halves round "up", toward zero)
Math.round(3.5);                          // 4
Math.trunc(-3.7);                         // -3
Math.floor(-7 / 2);                       // -4  same as Python's -7 // 2
-7 % 3;                                   // -1  % takes the sign of the dividend (Python gives 2)
```

Money: never use floats. Store integer cents (`number` is exact for whole numbers up to 2^53, or use `bigint`), or use a decimal library such as `decimal.js` in TypeScript and `Decimal` in Python.

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
* In TypeScript a `Map` compares object keys by reference, so an array used as a key does not work directly; the usual equivalent is a string key such as `` `${row},${col}` `` (see Maps and Sets in TypeScript).

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

## Arrays and Tuples in TypeScript

A TypeScript array plays the role of Python's list: ordered, mutable, and able to grow. A tuple type is an array whose length and element types are fixed in the type, which is how TypeScript models Python's tuples (at run time a tuple is still an ordinary array).

```ts
const lst: number[] = [3, 1, 4, 1, 5];        // also Array<number>

lst.push(9);                  // add to end
lst.unshift(0);               // add to the front
lst.splice(1, 0, 100);        // insert 100 at index 1 (splice(start, deleteCount, ...items))
lst.splice(lst.indexOf(1), 1);// remove the first matching value
lst.pop();                    // remove and return the last item
lst.shift();                  // remove and return the first item
lst.splice(2, 1);             // remove the item at index 2
lst.indexOf(4);               // find index of a value (-1 if missing)
lst.includes(4);              // true: membership test (Python: 4 in lst)
lst.at(-1);                   // last item (Python lst[-1])
lst.length;                   // len(lst)
lst.concat([7, 8]);           // new array with both (also [...lst, 7, 8])
lst.push(...[7, 8]);          // extend in place

// sorting: the DEFAULT sort compares as STRINGS
[10, 2, 1].sort();                          // [1, 10, 2]   surprise!
[10, 2, 1].sort((a, b) => a - b);           // [1, 2, 10]   always pass a comparator for numbers
[10, 2, 1].sort((a, b) => b - a);           // [10, 2, 1]   descending
const sorted = [...lst].sort((a, b) => a - b);   // sort() changes the array in place; copy first for sorted()
lst.toSorted((a, b) => a - b);              // returns a new array (ES2023; also toReversed, toSpliced, with)
lst.reverse();                              // reverse in place

// searching and testing
lst.find((x) => x > 3);                     // first match or undefined
lst.findIndex((x) => x > 3);
lst.some((x) => x > 3);                     // any([...])
lst.every((x) => x > 0);                    // all([...])
lst.filter((x) => x % 2 === 0).length;      // count occurrences that match
```

```ts
// Tuples: fixed length, a type for each position
const t: [number, string, boolean] = [1, "a", true];
const [a, b, c] = t;                         // destructuring (unpacking)
const t2 = [...t, 4] as const;               // "concatenation" makes a new array
// t[3];                                     // compile error: tuple of length 3 has no element at index 3
const frozen: readonly number[] = [1, 2, 3]; // readonly array: the compiler forbids push/sort/assignment
// frozen.push(4);                           // compile error: Property 'push' does not exist on type 'readonly number[]'
```

### Arrays Grow by Themselves

There is no capacity to manage and no "append may reallocate" rule: the JavaScript engine grows the array's storage for you, and `push` always changes the array you called it on. What you do need to know is which operations change the array in place and which return a new one.

```ts
const a = [1, 2, 3];
const b = a;                  // another name for the SAME array (like Python)
b.push(4);                    // a is [1, 2, 3, 4] too

const copy = [...a];          // shallow copy (also a.slice() or Array.from(a)); Python: a.copy() / a[:]
const nested = [[1, 2], [3, 4]];
const shallow = [...nested];  // inner arrays are still shared
const deep = structuredClone(nested);   // real deep copy
nested[0].push(99);
shallow[0];                   // [1, 2, 99]  shared
deep[0];                      // [1, 2]      independent

// In place (changes the array):  push pop shift unshift splice sort reverse fill
// New array (leaves it alone):   slice concat map filter flat flatMap toSorted toReversed toSpliced with
a.length = 0;                 // empties the array in place
const holes = new Array(3);   // [ <3 empty items> ]: holes, not undefined values (use .fill(0))
delete a[0];                  // leaves a HOLE and does not shift anything: use splice instead
```

Because arrays are objects, a function that receives an array can change the caller's array (`arr.push(1)` is visible outside), but reassigning the parameter (`arr = []`) is not. This is the same "pass by object reference" behavior as in Python.

```ts
function mutate(arr: number[]) { arr.push(1); }     // the caller sees the change
function rebind(arr: number[]) { arr = [9]; }       // only the local name is rebound
const data: number[] = [];
mutate(data);                                       // data is [1]
rebind(data);                                       // data is still [1]
```

Indexing past the end gives `undefined`, not an error (`lst[10]`). With `noUncheckedIndexedAccess` turned on in `tsconfig.json`, the compiler types `lst[i]` as `number | undefined` and makes you handle that case.

## Objects, Maps and Sets in TypeScript

TypeScript has two dictionary-like tools. A plain object (`{}`) is the everyday choice for records with known field names (like a Python dict used as a record). A `Map` is the real hash table: any type of key, insertion order, a `size`, and no prototype surprises.

```ts
// Plain object as a dict
const d: Record<string, number> = { a: 1, b: 2 };   // Record<string, number>: any string key, number values

d["c"] = 3;                       // insert or update (also d.c = 3)
d["z"];                           // undefined: a missing key does not throw (Python raises KeyError)
d["z"] ?? 0;                      // default value if key missing (Python: d.get("z", 0))
"b" in d;                         // true: membership test (Object.hasOwn(d, "b") is stricter)
delete d["a"];                    // remove a key
Object.keys(d);                   // ["b", "c"]
Object.values(d);                 // [2, 3]
Object.entries(d);                // [["b", 2], ["c", 3]]
Object.keys(d).length;            // len(d)
const merged = { ...d, e: 5 };    // merge / update (Python: d1 | d2)
Object.assign(d, { f: 6 });       // update in place

for (const [key, value] of Object.entries(d)) {
  console.log(key, value);
}

// Nested objects
const nested: Record<string, { name: string; age: number }> = {
  user1: { name: "Alice", age: 25 },
  user2: { name: "Bob", age: 30 },
};
nested["user1"].name;                          // "Alice"
nested["user2"].age = 31;                      // update a nested value
nested["user3"] = { name: "Cara", age: 22 };   // add a new nested entry
```

```ts
// Map: the real hash map
const m = new Map<string, number>([["a", 1], ["b", 2]]);

m.set("c", 3);                   // insert or update
m.get("z");                      // undefined when missing
m.get("z") ?? 0;                 // default
m.has("a");                      // true (needed when a stored value could itself be undefined)
m.delete("a");                   // returns true if the key existed
m.size;                          // number of entries (len)
for (const [k, v] of m) {        // iteration is in insertion order
  console.log(k, v);
}
[...m.keys()]; [...m.values()]; [...m.entries()];

// defaultdict(int) / Counter-style counting
const counts = new Map<string, number>();
for (const ch of "mississippi") {
  counts.set(ch, (counts.get(ch) ?? 0) + 1);
}
// Map(4) { 'm' => 1, 'i' => 4, 's' => 4, 'p' => 2 }

// Set
const s1 = new Set([1, 2, 3]);
const s2 = new Set([2, 3, 4]);
s1.add(10);
s1.has(10);                      // true
s1.delete(1);
s1.size;
const union = new Set([...s1, ...s2]);                       // {1,2,3,4}-style union
const inter = new Set([...s1].filter((x) => s2.has(x)));     // intersection
const diff  = new Set([...s1].filter((x) => !s2.has(x)));    // difference
// newer runtimes also have s1.union(s2), s1.intersection(s2), s1.difference(s2): check your Node version
const unique = [...new Set([1, 2, 2, 3])];                   // [1, 2, 3]  (remove duplicates)
```

### Key Constraints

* A plain object accepts only string or symbol keys: a number key such as `d[1]` is converted to the string `"1"`, and an object used as a key becomes `"[object Object]"`
* A plain object has a prototype, so `"toString" in {}` is `true`; use `Object.hasOwn(obj, key)`, or a `Map`, when keys come from user input
* A `Map` or `Set` accepts any value as a key, but compares objects and arrays by reference (identity), not by contents. That makes it the opposite of Python's tuple keys

```ts
const grid = new Map<[number, number], string>();
grid.set([0, 0], "start");
grid.get([0, 0]);                    // undefined!  a NEW array is a different key

// Fix 1: turn the composite key into a string
const key = (r: number, c: number) => `${r},${c}`;
const cells = new Map<string, string>([[key(0, 0), "start"], [key(2, 3), "wall"]]);
cells.get(key(2, 3));                // "wall"
cells.has(key(5, 5));                // false

// Fix 2: nested maps (or nested objects): sales.get("Delhi")?.get(2025)
const sales = new Map<string, Map<number, number>>();

// Fix 3: reuse ONE object instance as the key (identity is the point)
const origin = { x: 0, y: 0 };
const byPoint = new Map([[origin, "start"]]);
byPoint.get(origin);                 // "start"

new Map([[NaN, "x"]]).get(NaN);      // "x"  Map treats NaN as equal to NaN (unlike ===)
```

### Reference Behavior

Assigning or passing an object, array, `Map`, or `Set` copies only the reference, so every copy sees the same data. This matches Python.

```ts
function rename(m: Record<string, number>) { m["x"] = 99; }

const m1: Record<string, number> = { x: 1 };
const alias = m1;
rename(m1);
console.log(m1["x"], alias["x"]);          // 99 99

const shallow = { ...m1 };                 // shallow copy (new Map(map) and new Set(set) do the same)
const deep = structuredClone(m1);          // deep copy (works for Map and Set too)
```

`WeakMap` and `WeakSet` hold their object keys weakly: when nothing else refers to the key, the entry disappears. Use them for caches and metadata attached to objects without causing memory leaks. JavaScript runs your code on one thread, so the "concurrent map writes" crash does not exist, but operations spread across `await` points can still interleave (see Concurrency in TypeScript).

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

## Enums in TypeScript

TypeScript has an `enum` keyword, but many teams prefer a union of string literals or an `as const` object, because both are plain JavaScript with no extra generated code. You will see all three.

```ts
// 1. enum: numeric by default; the compiler generates a real object
enum Weekday {
  Sunday,       // 0
  Monday,       // 1
  Tuesday,      // 2
}
Weekday.Monday;             // 1
Weekday[1];                 // "Monday"   numeric enums also map backwards (value -> name)
// const w: Weekday = 7;   // compile error in TypeScript 5+ (but any plain number VARIABLE is still accepted: a known gap)

// string enum: values are strings (best for JSON/APIs); no reverse mapping
enum Status {
  Active = "active",
  Disabled = "disabled",
}
Status.Active;              // "active"
Object.values(Status);      // ["active", "disabled"]   iterate the members

// 2. union of string literals: no run-time code at all
type Direction = "up" | "down" | "left" | "right";
let dir: Direction = "up";
// dir = "sideways";        // compile error: Type '"sideways"' is not assignable to type 'Direction'

// 3. const object + derived type: a runtime list AND a type
const Color = { Red: 1, Green: 2, Blue: 3 } as const;
type Color = (typeof Color)[keyof typeof Color];      // 1 | 2 | 3
const c: Color = Color.Green;
Object.keys(Color);         // ["Red", "Green", "Blue"]
```

```ts
// Bit flags
enum Perm {
  Read = 1 << 0,            // 1
  Write = 1 << 1,           // 2
  Exec = 1 << 2,            // 4
}
const perm = Perm.Read | Perm.Write;
(perm & Perm.Write) !== 0;  // true: check a flag
perm & ~Perm.Write;         // clear a flag

enum Size { KB = 1024, MB = 1024 * 1024 }       // computed values are allowed
const enum Level { Debug = 10, Info = 20 }      // const enum: inlined at compile time, no object at run time
```

Use a union of string literals for simple sets of options, a string `enum` or `as const` object when you also need the values at run time, and avoid mixing numeric enums with raw numbers. Switch statements over a union or enum can be checked for completeness with a `never` assignment (see Conditionals).

> Start numeric enums at `1` (or give explicit values) when you want `0` to mean "unset", since `0` is also falsy.

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

## Equality in TypeScript

`===` (strict equality) compares primitives by value and everything else (objects, arrays, functions, `Map`s) by reference, so it works like Python's `is` for objects. There is no built-in `==`-style "same contents" check for arrays or objects. The loose `==` converts types first and should be avoided.

```ts
const p1 = { x: 1, y: 2 };
const p2 = { x: 1, y: 2 };
const p3 = p1;

p1 === p2;                    // false  two different objects, even with the same contents
p1 === p3;                    // true   another name for the same object
[1, 2] === [1, 2];            // false
"ab" === "a" + "b";           // true   primitives (string, number, boolean, ...) compare by value

1 === 1;                      // true
1 === "1";                    // false  no conversion with ===
1 == "1";                     // true   loose equality converts: avoid
null == undefined;            // true   (loose); null === undefined is false

NaN === NaN;                  // false  NaN is never equal to anything, even itself
Object.is(NaN, NaN);          // true   Object.is: like === except for NaN and -0
Object.is(0, -0);             // false
```

To compare contents you have to write it, serialize, or use a library:

```ts
import { isDeepStrictEqual } from "node:util";            // built into Node.js
import assert from "node:assert";

isDeepStrictEqual([1, [2, 3]], [1, [2, 3]]);              // true   deep value comparison
assert.deepStrictEqual({ a: 1 }, { a: 1 });               // throws if they differ (used in tests)
JSON.stringify(p1) === JSON.stringify(p2);                // true, but key order matters, and it drops undefined/functions
// libraries: lodash isEqual, fast-deep-equal

const same = (a: number[], b: number[]) =>
  a.length === b.length && a.every((v, i) => v === b[i]); // a hand-written comparison for simple arrays
```

You can give your own classes an `equals` method, but there is no `__eq__`: `===` always means identity for objects.

```ts
class Point {
  constructor(public x: number, public y: number) {}
  equals(other: Point): boolean { return this.x === other.x && this.y === other.y; }
}
new Point(1, 2).equals(new Point(1, 2));    // true
new Point(1, 2) === new Point(1, 2);        // false
```

TypeScript's type checker compares types by structure (see Type Systems), but at run time, equality of values is the JavaScript rules above.

## Assignment and Copying in TypeScript

Assigning a primitive (`number`, `string`, `boolean`, `bigint`, `null`, `undefined`) copies the value. Assigning an object or array copies the reference, so both names see the same data, as in Python. The copy tools are shallow unless noted:

```ts
const a = [[1, 2], [3, 4]];
const b = a;                          // same array
const shallow = [...a];               // shallow: new outer array, SAME inner arrays (also a.slice())
const shallowObj = { ...{ x: 1, nested: { y: 2 } } };   // shallow copy of an object
const deep = structuredClone(a);      // deep copy: handles nested data, Map, Set, Date; not functions or class methods
const viaJson = JSON.parse(JSON.stringify(a));          // old trick: loses Date, Map, undefined, functions

a[0].push(99);
shallow[0];                           // [1, 2, 99]  shared
deep[0];                              // [1, 2]      independent
```

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
* The heap holds data that must outlive a single call or whose size is not known up front. It needs a strategy to free memory: manual (C), garbage collected (Python, JavaScript/TypeScript, Java), or ownership rules checked at compile time (Rust).

In CPython every object lives on the heap; local variables are references to it. In TypeScript the JavaScript engine decides: objects, arrays, closures, and strings live on the heap, while small values such as numbers and booleans are often kept directly in registers or on the stack by the JIT compiler. You never choose between stack and heap in TypeScript code; you only choose between a primitive value and an object. The call stack is limited, though: very deep recursion ends with `RangeError: Maximum call stack size exceeded` (compare Python's `RecursionError`).

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
* TypeScript (V8, the engine in Node.js and Chrome): a generational tracing collector. New objects live in a small "young" area that is cleaned often and cheaply, survivors move to an "old" area that is cleaned with mark-and-sweep, mostly concurrently with your code. There is no reference counting, so cycles are never a problem. You tune it with Node flags such as `--max-old-space-size=4096` (heap limit in MB); you cannot normally force a collection (`global.gc()` exists only when Node is started with `--expose-gc`), and `process.memoryUsage()` reports usage.
* Python and TypeScript both promise you cannot use freed memory (no use-after-free) and do not need `free()`.

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
* TypeScript only: `setInterval` that is never cleared, `addEventListener` / `emitter.on` without a matching remove, a `Map` used as a cache that never evicts, and (in browsers) detached DOM nodes that something still references
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

```ts
const cache = new Map<number, User>();       // grows forever

async function getUser(id: number): Promise<User> {
  if (!cache.has(id)) {
    cache.set(id, await loadFromDb(id));
  }
  return cache.get(id)!;
}

// Fix: bound the cache (evict the oldest entry) or use a library such as lru-cache
const MAX = 1024;
function remember(id: number, user: User) {
  cache.set(id, user);
  if (cache.size > MAX) {
    const oldest = cache.keys().next().value!;   // Maps remember insertion order
    cache.delete(oldest);
  }
}

// A leaked timer keeps its callback (and everything it captured) alive until cleared
const timer = setInterval(() => poll(), 1000);
clearInterval(timer);                        // always clear what you start

const meta = new WeakMap<object, string>();  // entries vanish when the key object is collected
```

Finding leaks: Python has `tracemalloc` and `objgraph`; Node.js has `process.memoryUsage()`, heap snapshots (`node --inspect`, then Chrome DevTools "Memory", or `v8.writeHeapSnapshot()`), and `--heapsnapshot-signal`. If memory climbs steadily under constant load, something is leaking.

## Value vs Reference in TypeScript

Python hides memory addresses behind references, and so does TypeScript: there are no pointers, no `&`, and no pointer arithmetic. Instead the language splits values into two groups.

* Primitives (`number`, `string`, `boolean`, `bigint`, `symbol`, `null`, `undefined`) are immutable values that are copied on assignment and when passed to a function
* Everything else (objects, arrays, functions, `Map`, `Set`, class instances) is accessed through a reference; assigning or passing it copies the reference, and both names point at the same object

```ts
let x = 10;
let y = x;           // copies the value
y = 20;              // x is still 10

const a = { n: 1 };
const b = a;         // copies the reference
b.n = 2;             // a.n is 2 too

function incr(n: number) { n++; }                 // changes a local copy
function incrObj(o: { n: number }) { o.n++; }     // changes the caller's object

let v = 1;
incr(v);                                           // v === 1
const box = { n: 1 };
incrObj(box);                                      // box.n === 2
```

TypeScript always passes by value, where the value is either a primitive or a reference, which is the same "pass by object reference" rule as Python. To let a function change a primitive variable, return the new value (`v = incr(v)`) or wrap it in an object. `readonly` and `as const` stop accidental changes at compile time, and `Object.freeze` stops them at run time (one level deep).

```ts
interface Config { readonly host: string; port: number }
const cfg: Config = { host: "localhost", port: 8080 };
// cfg.host = "x";                 // compile error: Cannot assign to 'host' because it is a read-only property
const frozen = Object.freeze({ a: 1 });
// frozen.a = 2;                   // silently ignored (or a TypeError in strict mode); the compiler flags it too
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

## Built-in Functions in TypeScript

TypeScript (JavaScript) has few free-standing built-in functions. Most of the work is done by methods on arrays, strings, and numbers, and by global objects such as `Math`, `JSON`, `Object`, and `Number`.

```ts
[1, 2, 3].length;                               // 3 (a property, not a function)
Array.from({ length: 5 }, (_, i) => i);         // range(5)
["a", "b"].entries();                           // enumerate: (0,'a'), (1,'b')
typeof 5;                                       // "number"   (run-time type name)
Array.isArray([]);                              // true
(255).toString(16);                             // "ff"    hex(255)
(5).toString(2);                                // "101"   bin(5)
parseInt("ff", 16);                             // 255
Number.isInteger(5);                            // true
5 instanceof Number;                            // false (primitives are not instances); use typeof for primitives
[] instanceof Array;                            // true     instanceof is for objects and classes

// The Python built-ins, as array methods
const nums = [1, 2, 3, 4, 5];
const doubled = nums.map((x) => x * 2);                 // map
const evens = nums.filter((x) => x % 2 === 0);          // filter
const paired = nums.map((n, i) => [n, doubled[i]]);     // zip
const total = nums.reduce((acc, x) => acc + x, 0);      // sum / functools.reduce
nums.some((x) => x > 4);                                // any
nums.every((x) => x > 0);                               // all
[3, 1, 2].sort((a, b) => b - a);                        // sorted(..., key=lambda x: -x)  -> [3, 2, 1]
nums.slice().reverse();                                 // reversed
Math.max(...nums);                                      // max   (min, sum: Math.min / reduce)
console.log(doubled, evens, paired);

// Objects and JSON
Object.keys({ a: 1 });                                  // ["a"]
Object.entries({ a: 1 });                               // [["a", 1]]
Object.fromEntries([["a", 1]]);                         // { a: 1 }
JSON.stringify({ a: 1 });                               // '{"a":1}'
structuredClone({ a: [1] });                            // deep copy
```

```ts
// Other useful globals
setTimeout(() => console.log("later"), 1000);           // run once after 1000 ms (returns a handle: clearTimeout)
setInterval(() => console.log("tick"), 1000);           // run repeatedly (clearInterval)
queueMicrotask(() => {});                               // run right after the current code, before timers
console.log("a", { b: 1 });                             // print (also console.error, console.table, console.time)
crypto.randomUUID();                                    // a random UUID
typeof undefinedName;                                   // "undefined" without a ReferenceError (typeof is safe)
void 0;                                                 // undefined
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

## Functions in TypeScript

```ts
function greet(name: string, greeting = "Hello"): string {      // parameter types after names; default values allowed
  return `${greeting}, ${name}!`;
}

function minMax(nums: number[]): [number, number] {             // return a tuple (one array) and destructure it
  return [Math.min(...nums), Math.max(...nums)];
}
const [low, high] = minMax([3, 1, 2]);

function total(...nums: number[]): number {                     // rest parameter: nums is a number[] (Python *args)
  return nums.reduce((acc, n) => acc + n, 0);
}
total(1, 2, 3);
total(...[1, 2, 3]);                                            // spread an array into the arguments

// "keyword arguments": pass one object and destructure it, with defaults
function connect({ host, port, timeout = 5 }: { host: string; port: number; timeout?: number }) {
  return `${host}:${port} (${timeout}s)`;
}
connect({ host: "db", port: 5432, timeout: 10 });               // names make the call readable, order does not matter

function log(message: string, level?: string) {                 // optional parameter: type is string | undefined
  console.log(`[${level ?? "info"}] ${message}`);
}

// Function overloads: several call shapes, one implementation
function parse(x: string): number;
function parse(x: number): string;
function parse(x: string | number): number | string {
  return typeof x === "string" ? Number(x) : String(x);
}
```

```ts
// Function expressions, arrow functions (the TypeScript version of lambda), and functions as values
const add = (x: number, y: number): number => x + y;            // concise body: the value is returned
const square = (x: number) => x * x;                            // return type inferred
const makeObj = () => ({ ok: true });                           // wrap an object literal in () to return it
add(3, 4);                                                      // 7

function apply(f: (n: number) => number, v: number): number {   // a parameter whose type is a function
  return f(v);
}
apply(square, 4);                                               // 16
type Handler = (event: string) => void;                         // a named function type

// Closures
function outer(x: number) {
  return (y: number) => x + y;
}
const add5 = outer(5);
add5(10);                                                       // 15

// Generators
function* countdown(n: number): Generator<number> {
  while (n > 0) {
    yield n;
    n--;
  }
}
for (const num of countdown(3)) console.log(num);               // 3 2 1

// A decorator in the Python sense: a function that wraps a function
function logger<A extends unknown[], R>(fn: (...args: A) => R) {
  return (...args: A): R => {
    console.log(`Calling ${fn.name}`);
    return fn(...args);
  };
}
const sayHi = logger(function sayHi() { console.log("Hi!"); });
sayHi();
```

* TypeScript has no keyword arguments, but a single options object with destructuring gives the same readability; positional default values work too
* A function with no `return` statement returns `undefined` (Python: `None`); a return type of `void` documents that
* Python's mutable-default trap does not exist: default expressions are evaluated on every call, so `function f(items: number[] = [])` gets a fresh array each time
* `this` inside a regular `function` depends on how it is called, while an arrow function keeps the `this` of the place it was written (see Types, Interfaces and Classes)
* Return an error as a value or throw an exception for anything that can fail (see Exception Handling)

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

TypeScript has block scope for `let` and `const`: every `{ }` creates a scope, and a variable declared inside it disappears at the closing brace. Lookup is lexical, as in Python: inner scopes can read outer names, and the search goes outward. The old `var` keyword ignores blocks and is scoped to the whole function (and is "hoisted", visible as `undefined` before its line), which is why modern code never uses it.

```ts
function main() {
  const x = 1;
  if (true) {
    const y = 2;                 // y exists only inside this block
    console.log(x, y);
  }
  // console.log(y);             // compile error: Cannot find name 'y'

  for (var i = 0; i < 3; i++) {} // var leaks out of the loop, like Python
  console.log(i);                // 3
  for (let j = 0; j < 3; j++) {} // let does not
  // console.log(j);             // compile error: Cannot find name 'j'

  // console.log(early);         // compile error: Block-scoped variable 'early' used before its declaration
  const early = 5;               // the "temporal dead zone": let/const exist but cannot be used before this line
}
```

## Shadowing

Shadowing means an inner name hides an outer name that has the same spelling.

```python
len = 5                          # shadows the built-in len for the whole module
# len([1, 2])                    # TypeError: 'int' object is not callable
list = [1, 2]                    # a very common beginner bug: now list(...) no longer works
```

In TypeScript an inner `let` or `const` with the same name creates a new variable that hides the outer one inside its block, and the outer one is left unchanged. A common trap is intending to assign but declaring a new variable instead (the ESLint rule `no-shadow` flags it):

```ts
let result = "outer";
if (Math.random() > 0.5) {
  const result = "inner";          // NEW variable: shadows the outer one inside this block
  console.log(result);             // "inner"
}
console.log(result);               // still "outer": the value computed inside was lost

// Fix: assign to the outer variable instead of declaring a new one
let data = "none";
if (Math.random() > 0.5) {
  data = "found";                  // no let/const: assigns the outer variable
}

// Shadowing built-ins is possible too
const undefinedValue = undefined;
function demo(Number: string) { /* Number is now a string here, the global Number is hidden */ }
```

## Closures and Late Binding

A closure is a function that remembers variables from the scope where it was created, even after that scope has finished. It captures the variables themselves, not copies of their values at that moment.

```python
funcs = [lambda: i for i in range(3)]
[f() for f in funcs]             # [2, 2, 2]   every lambda sees the same i, whose final value is 2

funcs = [lambda i=i: i for i in range(3)]   # bind the current value as a default argument
[f() for f in funcs]             # [0, 1, 2]
```

```ts
function counter(): () => number {
  let count = 0;                   // captured by the closure
  return () => {
    count++;
    return count;
  };
}

const next = counter();
next();                            // 1
next();                            // 2

// Late binding with var: every callback shares ONE i, whose final value is 3
for (var i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 0);        // 3 3 3
}

// let creates a fresh variable for each iteration, so each closure keeps its own value
for (let j = 0; j < 3; j++) {
  setTimeout(() => console.log(j), 0);        // 0 1 2
}
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

`from module import name` makes a new name that points to the object at import time; if the module later rebinds its own variable, your name does not follow. In TypeScript each file that has an `import` or `export` is a module with its own scope, and nothing is visible to other files unless it is marked `export`. An imported name is a live, read-only view of the exporting module's variable, so if the exporter later changes it, importers see the new value (the opposite of Python's `from` behavior).

```ts
// file: counter.ts
export let count = 0;
export function increment() { count++; }

// file: main.ts
import { count, increment } from "./counter.js";   // named import (ESM paths in Node usually end in .js, even for .ts source)
import * as counter from "./counter.js";            // namespace import: counter.count, counter.increment
import math from "./math.js";                       // default import
increment();
console.log(count);                                 // 1   a live view
// count = 5;                                       // compile error: Cannot assign to 'count' because it is an import
```

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

```ts
function makeCounter() {
  let count = 0;                 // scope: makeCounter's body and the inner function
  return () => ++count;
}

const c = makeCounter();         // makeCounter has returned, its scope is gone
c(); c();                        // but the value behind count is still alive (2), because the arrow function references it
```

The opposite also happens: a name can still be in scope while the thing it refers to is finished with, such as a closed file object or a closed network connection. Scope never tells you whether a resource is still usable; cleanup rules (`with` in Python, `try`/`finally` or `using` in TypeScript) do.

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

TypeScript has no `__name__`. A file runs from top to bottom when Node starts it, and also when another file imports it, so a file that must work as both a library and a program needs a check. In CommonJS the check is `require.main === module`; in ES modules, newer Node versions provide `import.meta.main`, and otherwise you compare the file's own URL with the script Node was started with:

```ts
// file: tools.ts (ES module)
import { fileURLToPath } from "node:url";

export function helper(): string {
  return "helping";
}

function main(): void {
  console.log("running tools directly");
  console.log(helper());
}

if (process.argv[1] === fileURLToPath(import.meta.url)) {   // true only when this file is the program, not when imported
  main();
}
```

As in Python, the top-level code of a module runs once, the first time it is imported, and later imports reuse the loaded module. Keep the top level to definitions and keep side effects inside functions.

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

TypeScript accepts circular imports without complaint, which makes them easy to create by accident. ES modules handle a cycle with live bindings, but a value used before its module has finished evaluating is `undefined` or throws a `ReferenceError`, and the bug depends on which file is loaded first. The same fixes work: move shared code into a third file, import `type`-only names with `import type { Foo } from "./foo"` (erased at compile time, so no run-time cycle), or pass the dependency in as an argument. Tools such as the ESLint rule `import/no-cycle` and `madge` find cycles for you.

## Packages and Modules in TypeScript

A module is a single `.ts` file that contains `import` or `export`. A package is a folder with a `package.json` (a library you install from npm, or your own project). Imports are either relative paths (`./store`) or package names (`express`), and `export` marks what other files may use.

```text
myapp/
├── package.json                # "name": "myapp", "type": "module"
├── tsconfig.json
├── src/
│   ├── main.ts                 # entry point
│   ├── store/
│   │   ├── index.ts            # re-exports the public parts of the folder (like __init__.py)
│   │   └── store.ts
│   └── internal/
│       └── auth.ts
└── dist/                       # compiled JavaScript
```

```ts
// src/store/store.ts
export class Store {
  private items = new Map<string, string>();      // "private" is checked by the compiler only
  put(k: string, v: string): void { this.items.set(k, v); }
  get(k: string): string | undefined { return this.items.get(k); }
}

function helper() {}                               // not exported: private to this file
export default function createStore() { return new Store(); }   // one default export per file (optional)

// src/store/index.ts: the folder's public face
export { Store } from "./store.js";
export type { Config } from "./types.js";           // type-only export: erased at run time

// src/main.ts
import { Store } from "./store/index.js";           // relative path (with .js in Node's ESM mode)
import createStore from "./store/store.js";         // default import: you pick the name
import { readFile } from "node:fs/promises";        // built-in module (node: prefix)
import express from "express";                      // package from node_modules

const s = new Store();
s.put("a", "1");
```

* A file is private to itself until it uses `export`; there is no `__all__` and no underscore convention (use `export` selectively, and `private` or `#field` inside classes)
* `package.json` has `"type": "module"` for ES modules (`import`/`export`) or leaves it out for CommonJS (`require`/`module.exports`); use ES modules in new projects, and with `"module": "NodeNext"` in `tsconfig.json` write the `.js` extension in relative imports
* `import type { Foo }` imports only a type; it disappears from the compiled output
* A folder with an `index.ts` can be imported by the folder's name (the bundler/`moduleResolution` setting decides whether this works), much like `__init__.py` re-exports
* Path aliases (`"paths": { "@/*": ["src/*"] }` in `tsconfig.json`) shorten deep imports, but the runtime or bundler must also understand them
* `import "./polyfill.js"` imports a file only for its side effects

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

## Dependencies in TypeScript (npm)

Every TypeScript project is also an npm project. The file `package.json` lists the direct dependencies and the version range each may use. The file `package-lock.json` records the exact version of every package that was installed, including transitive ones, so everyone gets the same tree. The packages themselves are downloaded into a folder called `node_modules` inside the project, which is why there is no separate "virtual environment" step: the isolation is the folder.

```json
{
  "name": "myapp",
  "version": "1.0.0",
  "type": "module",
  "engines": { "node": ">=20" },
  "scripts": { "build": "tsc", "test": "vitest run" },
  "dependencies": {
    "express": "^4.19.2",
    "zod": "^3.23.0"
  },
  "devDependencies": {
    "typescript": "^5.5.0",
    "@types/node": "^20.14.0",
    "@types/express": "^4.17.21",
    "vitest": "^2.0.0"
  }
}
```

* `dependencies` are needed when the program runs; `devDependencies` are only needed to build and test (the compiler, test runner, linters, and `@types/*` packages)
* A library that is written in JavaScript needs type declarations before TypeScript can check your use of it: many ship their own, and for the rest install the community package `@types/<name>` (from DefinitelyTyped)
* `node_modules/` is never committed (put it in `.gitignore`); commit `package.json` and `package-lock.json`

### Version Ranges (semver)

```text
"express": "4.19.2"      exactly this version (a "pin")
"express": "^4.19.2"     caret: >=4.19.2 and <5.0.0   (the default npm writes; new features and fixes, no major bumps)
"express": "~4.19.2"     tilde: >=4.19.2 and <4.20.0  (patch updates only)
"express": ">=4.19.2"    this or newer
"express": "4.x"         any 4.something
"express": "^0.2.3"      for 0.x versions the caret is stricter: >=0.2.3 and <0.3.0
"express": "*"           anything (dangerous)
```

```bash
npm install                          # install everything in package.json (honours package-lock.json)
npm ci                               # clean, exact install from the lock file; fails if it disagrees with package.json (use in CI and Docker)
npm install express                  # add a dependency (latest), updates package.json and the lock file
npm install -D typescript            # add a dev dependency (--save-dev)
npm install express@4.19.2           # a specific version
npm uninstall express
npm update                           # move packages up to the newest versions their ranges allow
npm outdated                         # what has newer versions
npm ls                               # the dependency tree (npm ls zod: who uses it?)
npm explain zod                      # why is this package installed?
npm audit                            # check for known vulnerabilities (npm audit fix tries to repair)
npm dedupe                           # collapse duplicate copies of the same version
npx tsc --noEmit                     # run a tool installed in this project
npm run build                        # run a script from package.json
```

### Conflicts, Duplicates and Lock Files

Python allows one version of a package per environment, so two libraries that disagree cause `ResolutionImpossible`. npm can install more than one version of the same package side by side (each dependency gets its own copy in a nested `node_modules` when versions differ), so plain version conflicts are rare. The costs show up in other ways:

* `peerDependencies`: a plugin says "I work with React 18", and if the project has another version npm stops with `ERESOLVE unable to resolve dependency tree`; fix by aligning versions rather than forcing with `--force` or `--legacy-peer-deps`
* Duplicate copies make the install and the bundle bigger, and two copies of a library that must be a singleton (React, a state store) cause strange bugs; `npm ls pkg` and `npm dedupe` show and reduce them
* Types can conflict even when the code runs: two versions of `@types/node` or `@types/react` produce confusing compile errors
* Different lock files from different package managers (npm, Yarn, pnpm, Bun) fight each other; pick one per project
* Transitive packages are a supply-chain risk: commit the lock file, install with `npm ci`, run `npm audit`, keep the list of dependencies small, and avoid upgrading to a version published only hours ago

A project can pin its Node version with the `engines` field plus a `.nvmrc` file, and `pnpm`, `yarn`, and `bun` are alternative package managers that read the same `package.json`. For several packages in one repository use workspaces (`"workspaces": ["packages/*"]`), and for local development of a library use `npm link` or a `file:../mylib` dependency.

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

## API Calls in TypeScript

Modern JavaScript runtimes (Node.js 18+, Deno, Bun, and every browser) include `fetch`, so no third-party package is needed. Everything is asynchronous: `fetch` returns a promise, so you use `await`.

```ts
interface User {
  login: string;
  id: number;
}

async function getUser(name: string): Promise<User> {
  const response = await fetch(`https://api.github.com/users/${name}`, {
    signal: AbortSignal.timeout(5000),                 // fetch has NO default timeout: a hung request waits forever
  });

  if (!response.ok) {                                  // fetch does NOT throw for 4xx/5xx: check it yourself
    throw new Error(`unexpected status: ${response.status} ${response.statusText}`);
  }

  return (await response.json()) as User;              // json() returns any: the cast is a promise, not a check
}

console.log(await getUser("octocat"));                 // top-level await works in ES modules
```

```ts
// Query parameters
const params = new URLSearchParams({ q: "typescript", sort: "stars" });
const res = await fetch(`https://api.github.com/search/repositories?${params}`);
const data = await res.json();

// POST with a JSON body
const payload = { title: "Hello", body: "World", userId: 1 };
const created = await fetch("https://jsonplaceholder.typicode.com/posts", {
  method: "POST",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify(payload),                       // you serialize the body yourself
});
console.log(created.status);                           // 201
console.log(await created.json());

// Headers and authentication
await fetch("https://api.example.com/data", {
  headers: {
    Authorization: "Bearer YOUR_TOKEN",
    "Content-Type": "application/json",
  },
});

// PUT and DELETE
await fetch("https://jsonplaceholder.typicode.com/posts/1", {
  method: "PUT",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify({ title: "Updated" }),
});
await fetch("https://jsonplaceholder.typicode.com/posts/1", { method: "DELETE" });
```

```ts
// Handling errors and timeouts
async function load(url: string): Promise<unknown> {
  try {
    const response = await fetch(url, { signal: AbortSignal.timeout(5000) });
    if (!response.ok) {
      throw new Error(`HTTP error: ${response.status}`);
    }
    return await response.json();
  } catch (e) {
    if (e instanceof DOMException && e.name === "TimeoutError") {
      console.error("Request timed out");
    } else if (e instanceof TypeError) {
      console.error("Network failure:", e.message);     // fetch rejects with TypeError when the network fails
    } else {
      console.error("Request failed:", e);
    }
    throw e;
  }
}

// Several requests at the same time (Python: asyncio.gather)
const logins = ["octocat", "torvalds", "gaearon"];
const users = await Promise.all(logins.map((l) => getUser(l)));   // all start together; rejects if any one fails

// Validate data from outside instead of casting it (Zod is a popular schema library)
import { z } from "zod";
const UserSchema = z.object({ login: z.string(), id: z.number() });
const user = UserSchema.parse(await (await fetch("https://api.github.com/users/octocat")).json());
// user is now checked at run time AND typed: it throws if the shape is wrong
```

```ts
// A tiny server (the TypeScript version of the Flask example), using Express
import express from "express";

const app = express();
app.use(express.json());                                // parse JSON request bodies

app.get("/hello", (_req, res) => {
  res.json({ message: "Hello, World!" });
});

app.post("/add", (req, res) => {
  const { a, b } = req.body as { a: number; b: number };
  if (typeof a !== "number" || typeof b !== "number") {
    res.status(400).json({ error: "bad json" });
    return;
  }
  res.json({ result: a + b });
});

app.listen(8080, () => console.log("listening on :8080"));   // one thread handles all requests, using the event loop
```

```ts
// The same /hello with only Node's built-in http module (no packages)
import { createServer } from "node:http";

createServer((req, res) => {
  if (req.method === "GET" && req.url === "/hello") {
    res.writeHead(200, { "Content-Type": "application/json" });
    res.end(JSON.stringify({ message: "Hello, World!" }));
    return;
  }
  res.writeHead(404).end();
}).listen(8080);
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

## Exceptions in TypeScript

TypeScript uses the same model as Python: `throw` an error, and it unwinds the call stack until a `try`/`catch` handles it. If none does, Node.js prints the error and exits with a non-zero status. The differences are that you can throw any value, there are no checked exceptions (a function's type says nothing about what it can throw), and one `catch` block catches everything, so you inspect the error yourself.

```ts
function parseAge(text: string): number {
  const n = Number(text);
  if (Number.isNaN(n)) {
    throw new Error(`invalid age: ${text}`);       // Number("abc") gives NaN, not an exception: you check and throw
  }
  return n;
}

function loadUser(text: string) {
  return { age: parseAge(text) };                  // no try here: the error passes straight through
}

function main() {
  try {
    const user = loadUser("abc");
    console.log(user);
  } catch (e) {                                     // e is `unknown` in strict mode: anything can be thrown
    if (e instanceof Error) {
      console.log("Please enter a number:", e.message);
    } else {
      throw e;                                      // not an Error object: do not hide it
    }
  } finally {
    console.log("done");                            // ALWAYS runs: success, handled error, return, or crash
  }
}
```

Handle an error where you can do something meaningful about it (ask again, use a default, report to the user). In between, let it propagate.

## try, catch, finally, throw

```ts
import { readFileSync } from "node:fs";

function readNumber(path: string): number | null {
  try {                                             // code that may fail goes in try
    const value = Number(readFileSync(path, "utf-8"));
    if (Number.isNaN(value)) throw new RangeError("not a number");
    return value;
  } catch (e) {                                     // ONE catch for every kind of error
    if (e instanceof Error && "code" in e && e.code === "ENOENT") {   // Node's file errors carry a code
      console.log("file missing");
      return null;
    }
    if (e instanceof RangeError) {                  // pick handlers with instanceof (Python: several except clauses)
      console.log(`bad content: ${e.message}`);
    }
    throw e;                                        // re-throw (Python: bare raise)
  } finally {
    console.log("done");                            // ALWAYS runs: success, handled error, return, or crash
  }
}
```

* `try` — the code that might fail; keep it as short as possible
* `catch (e)` — handles an error; in strict mode `e` is `unknown`, so narrow it with `instanceof Error` (or a check on `e.name`/`e.code`) before using `e.message`
* No `else` block: put code that must run only on success after the risky statement inside `try`, or move the risky call into its own small function
* `finally` — cleanup that must happen no matter what (closing, unlocking, rolling back)
* `throw` — throw an error: `throw new TypeError("msg")`, or `throw e` inside `catch` to re-throw
* `new Error("...", { cause: e })` — wrap a lower-level error while keeping the original as the cause (Python: `raise ... from e`)

```ts
// Error chaining: translate low-level errors into your application's language
try {
  config = JSON.parse(text);
} catch (e) {
  throw new Error("invalid config file", { cause: e });   // e is kept in error.cause
}
```

A `return` inside `finally` overrides any thrown error or return value from the `try`, which is almost never what you want, exactly as in Python. You can throw anything (`throw "oops"`), but always throw `Error` objects (or subclasses): only they carry a stack trace.

## The Error Hierarchy

```text
Error                                    # base for normal errors; has message, name, stack, cause
├── TypeError                            # wrong type of value:  undefined.x, calling a non-function
├── RangeError                           # value out of range:   new Array(-1), "Maximum call stack size exceeded"
├── SyntaxError                          # bad syntax:           JSON.parse("abc")
├── ReferenceError                       # name does not exist:  using an undeclared variable
├── EvalError, URIError                  # rare
└── AggregateError                       # several errors in one (Promise.any, Promise.allSettled use)
Node adds: system errors with a `code` such as ENOENT (missing file), EACCES, ECONNREFUSED
Browsers add: DOMException (for example AbortError, TimeoutError)
```

There is no `KeyboardInterrupt` object: Ctrl+C is a signal you can listen for (`process.on("SIGINT", ...)`). Catch the narrowest case you can, and keep a catch-everything handler only at the outermost layer of the program.

## Validation

Validate data where it enters your program (user input, files, network, environment variables) and fail early with a clear message. Inside, trust the data you already checked. TypeScript's types vanish at run time, so the data from outside needs a real run-time check.

```ts
function setAge(age: unknown): number {
  if (typeof age !== "number" || Number.isNaN(age)) {
    throw new TypeError("age must be a number");
  }
  if (age < 0 || age > 150) {
    throw new RangeError(`age out of range: ${age}`);
  }
  return age;
}

// Two styles for handling possible failure
// LBYL: look before you leap
if ("key" in data) {
  value = data["key"];
}
// EAFP: try and handle the failure (less common in TypeScript, because a missing key does not throw)
value = data["key"] ?? null;                         // usually the natural form

// A schema library validates and types in one step
import { z } from "zod";
const Age = z.number().int().min(0).max(150);
Age.parse(30);                                       // 30; throws a ZodError for anything else
const result = Age.safeParse("x");                   // { success: false, error } instead of throwing
```

`assert` from `node:assert` is for internal assumptions that must always be true (`assert(items.length > 0)`); it is a real check (not removed in production), but it is still meant for bugs, not for validating user input. A type guard (`function isUser(x: unknown): x is User`) lets you check unknown data once and have the compiler treat it as the narrower type afterwards.

## Cleanup After Failure

Resources (files, locks, sockets, database connections, temporary files) must be released even when something fails halfway. In TypeScript use `try`/`finally`, or the newer `using` declaration (TypeScript 5.2+, for objects that implement `Symbol.dispose` or `Symbol.asyncDispose`; it needs a runtime or library that supports them), which calls the cleanup on the way out like Python's `with`.

```ts
import { open } from "node:fs/promises";

const file = await open("data.txt");
try {
  await process(file);
} finally {
  await file.close();                  // closed even if process() throws
}

// `await using` (async cleanup): the file is closed when the block ends, however it ends
{
  await using f = await open("data.txt");   // FileHandle implements Symbol.asyncDispose in recent Node versions
  await process(f);
}

// Write your own disposable resource
class Transaction implements AsyncDisposable {
  constructor(private conn: Connection) {}
  async commit() { await this.conn.exec("COMMIT"); }
  async [Symbol.asyncDispose]() { await this.conn.close(); }
}

// Without `using`, the transaction pattern is try / catch / finally
async function transaction(conn: Connection, work: () => Promise<void>) {
  try {
    await work();
    await conn.exec("COMMIT");          // success path
  } catch (e) {
    await conn.exec("ROLLBACK");        // failure path: undo the partial work
    throw e;                            // and still report the error
  } finally {
    await conn.close();
  }
}
```

## Never Silently Swallow Errors

An error that is caught and thrown away turns into a mystery later: wrong data, missing records, a program that "works" but is wrong.

```ts
// Bad: hides every problem
try {
  await save(order);
} catch {}

// Bad: catches too much and returns a value that looks like success
function getPrice(item: string): number {
  try {
    return prices[item];
  } catch {
    return 0;
  }
}

// Good: specific, logged with the stack, and the caller still learns about the failure
try {
  await save(order);
} catch (e) {
  console.error(`could not save order ${order.id}`, e);   // logs the message and the stack
  throw e;
}
```

* Catch specific errors (`instanceof`, `e.name`, `e.code`)
* Either handle an error or let it propagate; do not log it and also pretend it did not happen
* Add context when re-throwing (`{ cause: e }`), never delete it
* Log once, at the layer that finally handles the error, to avoid duplicate noise
* A promise that rejects and is never awaited or caught becomes an "unhandled rejection", which crashes recent Node versions by default; always `await` your promises or attach `.catch`

## Custom Errors

```ts
class AppError extends Error {
  constructor(message: string, options?: ErrorOptions) {
    super(message, options);
    this.name = new.target.name;          // so logs show "NotFoundError" instead of "Error"
  }
}

class NotFoundError extends AppError {
  constructor(public resource: string, public resourceId: number) {
    super(`${resource} ${resourceId} not found`);
  }
}

class ValidationError extends AppError {}

try {
  throw new NotFoundError("user", 42);
} catch (e) {
  if (e instanceof AppError) {            // one check for everything the app throws deliberately
    console.log(e.message);
  } else {
    throw e;
  }
}
```

## Errors in Promises and async/await

Inside an `async` function, `throw` rejects the returned promise, and `await` turns a rejection back into a throw, so ordinary `try`/`catch` works across `await`.

```ts
async function fetchJson(url: string): Promise<unknown> {
  const res = await fetch(url);
  if (!res.ok) throw new Error(`HTTP ${res.status}`);
  return res.json();
}

try {
  const data = await fetchJson("https://example.com/data");
} catch (e) {
  console.error("failed:", e);            // catches both the network error and our own throw
}

fetchJson("https://example.com/data").catch((e) => console.error(e));   // the promise style

// Several promises: choose how failures are handled
await Promise.all([fetchJson("a"), fetchJson("b")]);           // rejects at the first failure
const results = await Promise.allSettled([fetchJson("a"), fetchJson("b")]);
// [{ status: "fulfilled", value }, { status: "rejected", reason }]: nothing is thrown, you inspect each one
await Promise.any([fetchJson("a"), fetchJson("b")]);           // first success wins; AggregateError if all fail

// Forgetting await: the error escapes the try block
try {
  fetchJson("bad");                        // no await: a rejection here is NOT caught by this catch
} catch {}
```

## Expected Failures as Values

Throwing is for the unexpected. For failures that are an ordinary part of the logic (a lookup that finds nothing, a validation that fails), many TypeScript programs return the failure as a value, so the compiler forces the caller to deal with it. A discriminated union makes this safe:

```ts
type Result<T, E = Error> =
  | { ok: true; value: T }
  | { ok: false; error: E };

function parsePort(text: string): Result<number, string> {
  const n = Number(text);
  if (!Number.isInteger(n) || n < 1 || n > 65535) {
    return { ok: false, error: `bad port: ${text}` };
  }
  return { ok: true, value: n };
}

const r = parsePort("8080");
if (r.ok) {
  console.log(r.value);        // narrowed: value exists here
} else {
  console.log(r.error);        // narrowed: error exists here
}

const found = items.find((i) => i.id === 7);   // T | undefined is the simplest "maybe"
if (!found) { /* handle missing */ }
```

## try, except, raise, finally in TypeScript Terms

| Python | TypeScript |
| :--- | :--- |
| `try` / `except` | `try` / `catch (e)`, then `instanceof` or other checks to choose |
| `else` (success path) | the rest of the `try` block, or a separate small function |
| `raise Error(...)` | `throw new Error(...)` |
| `raise ... from e` | `new Error("...", { cause: e })` |
| `except SomeError` | `if (e instanceof SomeError)` |
| `finally` / `with` | `finally` or `using` / `await using` |
| bug that should crash | `throw`, or `assert(...)` from `node:assert` |
| custom exception class | `class MyError extends Error` |
| `KeyError` for a missing key | no error: a missing key reads as `undefined` |
| expected failure | return `T \| undefined`, or a `Result` union |

## Recovering from Failed Operations in TypeScript

The same ideas apply: retry only transient errors, limit attempts, back off, respect cancellation, and wrap the final error.

```ts
async function retry<T>(
  fn: () => Promise<T>,
  attempts = 3,
  baseDelayMs = 500,
  signal?: AbortSignal,
): Promise<T> {
  let lastError: unknown;
  for (let attempt = 1; attempt <= attempts; attempt++) {
    try {
      return await fn();
    } catch (e) {
      lastError = e;
      if (attempt === attempts) break;                          // out of attempts
      const delay = baseDelayMs * 2 ** (attempt - 1) + Math.random() * 100;   // 0.5s, 1s, 2s ... plus jitter
      await new Promise<void>((resolve, reject) => {
        const timer = setTimeout(resolve, delay);
        signal?.addEventListener("abort", () => {               // stop waiting if the caller gave up
          clearTimeout(timer);
          reject(signal.reason);
        }, { once: true });
      });
    }
  }
  throw new Error(`failed after ${attempts} attempts`, { cause: lastError });
}

const data = await retry(() => fetchJson("https://example.com/data"));
```

The outermost layer of an application is where unexpected errors end up: log them, show a clean message, and exit with a non-zero code so schedulers and CI know it failed.

```ts
async function main(): Promise<void> {
  // ...
}

main().catch((e) => {
  if (e instanceof AppError) {
    console.error(e.message);                   // expected failure: short message, no stack
    process.exitCode = 1;
  } else {
    console.error("unexpected failure", e);     // unexpected: full stack in the log
    process.exitCode = 2;
  }
});

process.on("unhandledRejection", (reason) => {   // last line of defence for forgotten promises
  console.error("unhandled rejection", reason);
  process.exit(1);
});
process.on("SIGINT", () => process.exit(130));   // conventional exit code for Ctrl+C
```

Set `process.exitCode` (and let the program finish) rather than calling `process.exit()` in the middle of the work, which can cut off pending output and cleanup.

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

## File Handling in TypeScript

Files are handled by Node.js's `node:fs` module (the same code does not work in a browser). Prefer the promise-based `node:fs/promises`, which fits `async`/`await`; the older callback API and the blocking `*Sync` versions (`readFileSync`) also exist, and the synchronous ones are fine in small scripts but block the event loop in a server.

```ts
import { readFile, writeFile, appendFile, mkdir, stat, rename } from "node:fs/promises";
import { createReadStream } from "node:fs";
import { createInterface } from "node:readline";
import path from "node:path";

// Small files: one call each
const text = await readFile("data.txt", "utf-8");            // string (without "utf-8" you get a Buffer of bytes)
await writeFile("out.txt", "hello", "utf-8");                // creates or truncates (like mode "w")
await appendFile("app.log", "one more line\n", "utf-8");     // like mode "a"
await writeFile("new.txt", "x", { flag: "wx" });             // like mode "x": fails if the file exists

// Larger files: stream line by line (like `for line in f`)
const rl = createInterface({ input: createReadStream("data.txt", "utf-8"), crlfDelay: Infinity });
for await (const line of rl) {
  console.log(line);
}

// Paths and checks
const p = path.join("logs", "app.log");                      // portable path building (Python: Path / ...)
await mkdir(path.dirname(p), { recursive: true });           // mkdir(parents=True, exist_ok=True)
path.extname(p);                                             // ".log"
path.basename(p);                                            // "app.log"

try {
  await stat(p);                                             // throws ENOENT when missing
} catch (e) {
  if ((e as NodeJS.ErrnoException).code === "ENOENT") {
    console.log("does not exist");
  } else {
    throw e;
  }
}

// JSON file <-> object
const config = JSON.parse(await readFile("config.json", "utf-8"));      // validate it before trusting it
await writeFile("out.json", JSON.stringify(config, null, 2), "utf-8");  // indent 2, like json.dump(indent=2)

// Safe replacement of a file: write a temp file, then swap it in, so a crash never leaves half a file
const tmp = "data.txt.tmp";
await writeFile(tmp, "new content", "utf-8");
await rename(tmp, "data.txt");                               // atomic on the same filesystem
```

* Always pass the encoding for text (`"utf-8"`), or you receive raw bytes
* `readFile` loads the whole file into memory; use streams or `readline` for big files
* There is no `with`: the promise helpers (`readFile`, `writeFile`) open and close for you; for a manual `FileHandle` use `try`/`finally` or `await using` (see Cleanup After Failure)
* Relative paths are resolved against the folder you started Node in (`process.cwd()`), not against the source file; in ES modules build a file-relative path with `new URL("./data.txt", import.meta.url)`

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
| Worker thread (TypeScript / Node.js) | Separate heap per worker; messages are copied (or memory is shared explicitly with `SharedArrayBuffer`) | Operating system | CPU-bound work without leaving the process |

Shared memory is fast and convenient but dangerous: two threads changing the same data need synchronization. Separate processes cannot damage each other's memory, but sharing data between them costs serialization and copying.

## The GIL (Global Interpreter Lock)

CPython has a lock that lets only one thread execute Python bytecode at a time. It protects the interpreter's internals (such as reference counts), which keeps the implementation simple and fast for single-threaded code.

* Threads still help I/O-bound programs: the GIL is released while a thread waits for the network or disk
* Threads do not speed up CPU-bound pure Python code: four threads share one core's worth of Python execution
* C extensions such as NumPy release the GIL during heavy computation, so they can use several cores from threads
* Python 3.13 added an optional "free-threaded" build without the GIL (experimental there, officially supported from 3.14, and not the default build), but libraries need to support it
* JavaScript has no GIL, but a stricter rule: each JavaScript environment (the main thread, or one worker) runs your code on exactly one thread, so there are no data races on ordinary variables; parallelism comes from separate worker threads (each with its own heap) or separate processes

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

# Concurrency in TypeScript

TypeScript runs as JavaScript, which has a different concurrency model from Python threads: one thread runs your code, and an event loop switches between tasks whenever one of them waits (`await`). This is the same idea as Python's `asyncio`, built into the language and used by every library. Real parallelism is available through worker threads.

## The Event Loop

Your JavaScript runs on a single thread. When you start something slow (a network call, a file read, a timer), the runtime does the waiting in the background and queues a callback for when it finishes. The loop runs queued callbacks one at a time, each to completion. Promise callbacks (microtasks) run before timers and I/O callbacks (macrotasks).

```ts
console.log("1 start");
setTimeout(() => console.log("4 timeout"), 0);          // macrotask: runs after the current code and all microtasks
Promise.resolve().then(() => console.log("3 microtask"));   // microtask: runs right after the current code
console.log("2 end");
// 1 start
// 2 end
// 3 microtask
// 4 timeout
```

Because only one callback runs at a time, a long synchronous computation blocks everything: timers, incoming requests, and other promises all wait.

```ts
function blockFor(ms: number) {
  const end = Date.now() + ms;
  while (Date.now() < end) {}                           // nothing else can run during this loop
}
setTimeout(() => console.log("timer"), 10);
blockFor(1000);                                         // the timer fires only after about 1000 ms
```

The rule is the same as Python's asyncio: never block the loop with heavy computation or blocking calls (`readFileSync` in a server, `crypto.pbkdf2Sync`); use async APIs, or move CPU work to a worker thread.

## Promises and async/await

A `Promise<T>` is a value that will arrive later. An `async` function always returns a promise; `await` pauses that function (not the thread) until the promise settles, and other code runs meanwhile. Calling an async function starts it immediately, running until its first `await`.

```ts
const sleep = (ms: number) => new Promise<void>((resolve) => setTimeout(resolve, ms));

async function sayHello(): Promise<void> {
  console.log("Start");
  await sleep(1000);                // non-blocking wait
  console.log("End");
}

await sayHello();                   // top-level await works in ES modules
```

```ts
// Running tasks concurrently
async function fetchData(name: string, delayMs: number): Promise<string> {
  await sleep(delayMs);
  console.log(`${name} done`);
  return name;
}

const results = await Promise.all([
  fetchData("Task1", 2000),
  fetchData("Task2", 1000),
  fetchData("Task3", 3000),
]);
console.log(results);               // finishes in about 3 s, not 6 s

// Awaiting one after another is sequential: 6 s
await fetchData("A", 2000);
await fetchData("B", 1000);
await fetchData("C", 3000);

// Start now, await later (Python: create_task)
const task1 = fetchData("X", 1000);   // already running
const task2 = fetchData("Y", 1000);
console.log(await task1, await task2);
```

A promise that is created but never awaited ("floating") is a common bug: its errors become unhandled rejections and the work can outlive the code that started it. Enable the ESLint rule `@typescript-eslint/no-floating-promises` to catch it.

```ts
// async iteration (Python: async for)
async function* ticker(): AsyncGenerator<number> {
  for (let i = 0; i < 3; i++) {
    await sleep(500);
    yield i;
  }
}
for await (const value of ticker()) {
  console.log(value);               // 0 1 2
}
```

The program does not end when the function that started the work returns: Node.js keeps running while there is pending work (timers, sockets, unresolved I/O), and exits when the event loop is empty. `process.exit()` stops it immediately, abandoning anything in flight.

## Combining Promises

| Combinator | Result | Use it when |
| :--- | :--- | :--- |
| `Promise.all([...])` | array of all values; rejects at the first failure | all of them must succeed (Python `gather`) |
| `Promise.allSettled([...])` | array of `{ status, value / reason }`; never rejects | you want every outcome, good or bad (`gather(return_exceptions=True)`) |
| `Promise.race([...])` | the first one to settle, success or failure | timeouts and "whichever answers first" |
| `Promise.any([...])` | the first success; `AggregateError` if all fail | redundant sources |

```ts
const settled = await Promise.allSettled([fetchData("a", 100), Promise.reject(new Error("boom"))]);
for (const r of settled) {
  if (r.status === "fulfilled") console.log("ok", r.value);
  else console.log("failed", r.reason.message);
}

// A timeout by racing (note: the loser keeps running; use AbortSignal to really cancel it)
const withTimeout = <T,>(p: Promise<T>, ms: number) =>
  Promise.race([p, new Promise<never>((_, reject) => setTimeout(() => reject(new Error("timeout")), ms))]);
await withTimeout(fetchData("slow", 5000), 2000);   // rejects after 2 s
```

## Limiting Concurrency (Semaphore, Worker Pool)

`Promise.all(items.map(...))` starts everything at once, which is a problem for 10 000 URLs or a database with a connection limit. JavaScript has no built-in semaphore, but a small pool of workers that pull from a shared index does the same job (the `p-limit` and `p-queue` libraries package this).

```ts
async function mapLimit<T, R>(
  items: T[],
  limit: number,
  fn: (item: T, index: number) => Promise<R>,
): Promise<R[]> {
  const results: R[] = new Array(items.length);
  let next = 0;

  async function worker(): Promise<void> {
    while (next < items.length) {
      const i = next++;                       // safe: only one thread, and no await between the read and the increment
      results[i] = await fn(items[i], i);
    }
  }

  await Promise.all(Array.from({ length: Math.min(limit, items.length) }, worker));
  return results;
}

const squares = await mapLimit([1, 2, 3, 4, 5, 6, 7, 8], 3, async (n) => {
  await sleep(100);
  return n * n;                               // at most 3 run at a time; results keep the input order
});
```

## Worker Threads: Real Parallelism

A worker thread (`node:worker_threads`) is a separate JavaScript environment with its own event loop and heap, running on its own core. Workers do not share ordinary variables: they talk by messages, which are copied (structured clone). This is the CPU-bound answer in TypeScript, like Python's `ProcessPoolExecutor`.

```ts
// main.ts
import { Worker } from "node:worker_threads";

function runWorker(n: number): Promise<number> {
  return new Promise((resolve, reject) => {
    const worker = new Worker(new URL("./worker.js", import.meta.url), { workerData: n });   // the compiled file
    worker.once("message", resolve);                    // the result arrives as a message
    worker.once("error", reject);                       // an exception inside the worker
    worker.once("exit", (code) => {
      if (code !== 0) reject(new Error(`worker stopped with code ${code}`));
    });
  });
}

const results = await Promise.all([10_000_000, 10_000_000, 10_000_000, 10_000_000].map(runWorker));
console.log(results);                                    // four jobs on four cores, in parallel
```

```ts
// worker.ts
import { parentPort, workerData } from "node:worker_threads";

let sum = 0;
for (let i = 0; i < (workerData as number); i++) {
  sum += Math.sqrt(i);                                   // CPU-bound work: it does not block the main thread
}
parentPort!.postMessage(sum);
```

* Starting a worker is slower than starting a promise; for many jobs keep a pool of workers alive (the `piscina` library)
* Messages are copied; to hand over a big buffer without copying, transfer it (`postMessage(buf, [buf.buffer])`) or share memory with a `SharedArrayBuffer`
* For separate processes (isolation, crash safety, other programs) use `child_process.fork` or the `cluster` module, the same idea as Python's `multiprocessing`
* In Python the matching trick is `asyncio.to_thread`; in Node.js the matching trick is a worker, because the main thread must stay free

## Message Passing vs Shared Memory

Worker threads can communicate by messages (the default) or by sharing memory.

* Message passing: each side owns its data and sends copies; there are no races, and it is the simplest model. A `MessagePort`/`postMessage` pair, an `EventEmitter`, or an async iterator (`for await`) are the usual tools.
* Shared memory: `SharedArrayBuffer` plus typed arrays lets several workers see the same bytes. Plain reads and writes are then unsafe, so use `Atomics` for counters and flags, and `Atomics.wait`/`notify` for waiting (only inside workers).

```ts
// producer / consumer with an async iterator: one side yields, the other awaits
async function* produce(n: number): AsyncGenerator<number> {
  for (let i = 0; i < n; i++) yield i;
}
for await (const v of produce(3)) {
  console.log(v);                                        // 0 1 2
}

// shared memory between workers: an atomic counter
const shared = new SharedArrayBuffer(4);
const counter = new Int32Array(shared);
Atomics.add(counter, 0, 1);                              // safe increment from any thread
Atomics.load(counter, 0);
// counter[0]++                                          // NOT atomic: lost updates when two workers do it
```

## Race Conditions in TypeScript

Because one thread runs your code, ordinary variables cannot be corrupted by two threads at once, and the "concurrent map writes" crash does not exist. Race conditions still happen across `await`: any `await` lets other code run, so a "check, then act" pair that spans an `await` can be interleaved.

```ts
let balance = 100;

async function withdraw(amount: number): Promise<void> {
  if (balance >= amount) {          // check
    await audit(amount);            // other code runs here!
    balance -= amount;              // act: balance may have changed meanwhile
  }
}

await Promise.all([withdraw(80), withdraw(80)]);
console.log(balance);               // -60: both passed the check before either subtracted
```

Fixes: keep the check and the update together with no `await` between them (the code between two `await`s runs without interruption), or run the critical section through a small promise-based lock so that one caller at a time enters:

```ts
class Mutex {
  private tail: Promise<unknown> = Promise.resolve();

  run<T>(fn: () => Promise<T>): Promise<T> {
    const result = this.tail.then(fn);        // start fn only after the previous holder finished
    this.tail = result.catch(() => {});       // keep the chain alive even if fn fails
    return result;
  }
}

const lock = new Mutex();
async function safeWithdraw(amount: number) {
  await lock.run(async () => {
    if (balance >= amount) {
      await audit(amount);
      balance -= amount;                      // check and update are now protected
    }
  });
}
```

A true data race (two threads touching the same memory with no ordering) is possible only with `SharedArrayBuffer`; use `Atomics`. There is no built-in race detector: you find races with careful design, tests that run operations concurrently (`Promise.all`), and by keeping shared state to a minimum.

## Deadlocks and Hangs in TypeScript

A "deadlock" in JavaScript is usually a promise that never settles, so the code awaiting it never continues.

```ts
await new Promise<void>(() => {});          // never resolved: this await waits forever
// (if nothing else keeps the event loop busy, Node exits silently; a pending timer or socket keeps it running, so it just hangs)

async function a(): Promise<void> { await b(); }
async function b(): Promise<void> { await a(); }    // a waits for b, b waits for a: infinite recursion or a stuck chain

// A lock that is waited on from inside itself can never be acquired
await lock.run(async () => {
  await lock.run(async () => {});            // waits for the outer run to finish, which waits for this: stuck forever
});
```

Prevent them with timeouts on everything that waits (`Promise.race` with a timer, `AbortSignal.timeout()`), by never re-entering a lock you already hold, by acquiring multiple locks in one fixed order, and by making sure every promise you create is resolved or rejected on every path. To investigate a hang, run with `node --inspect` and pause in DevTools, or log `process.getActiveResourcesInfo()` to see what keeps the process alive.

## Shared State Guidelines

* Prefer not sharing: give each task its own data, or pass data as arguments and return values
* Keep "check, then act" sequences free of `await` in the middle, or protect them with a lock
* Keep shared module-level state small and well named; a `Map` used as a cache is shared by every request
* Treat data that is never modified after creation (`readonly`, `Object.freeze`, `as const`) as safe
* Do not start a promise or timer unless you know how and when it will finish (and who handles its error)
* Share memory between workers only through `SharedArrayBuffer` with `Atomics`, and prefer messages

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

## AbortController and AbortSignal in TypeScript

A promise cannot be cancelled from outside, so JavaScript uses a cooperative signal, the same idea as Python's `threading.Event` flag. An `AbortController` creates an `AbortSignal`; calling `controller.abort()` marks the signal as aborted and fires its `abort` event. Everything that was given the signal (fetch, file operations, timers, your own code) checks it and stops.

```ts
const controller = new AbortController();
const { signal } = controller;

signal.addEventListener("abort", () => console.log("cancelled:", signal.reason));
controller.abort();                                   // or controller.abort(new Error("user pressed stop"))
signal.aborted;                                       // true
signal.reason;                                        // why it was aborted (an AbortError DOMException by default)

const timeoutSignal = AbortSignal.timeout(2000);      // aborts itself after 2 s, with a TimeoutError reason
const either = AbortSignal.any([controller.signal, AbortSignal.timeout(5000)]);   // aborted when ANY of them aborts
signal.throwIfAborted();                              // throws the reason if the signal is already aborted
```

Conventions: a function that can be cancelled takes a `signal?: AbortSignal` (often inside an options object), passes it down to everything that can block, and does not store it for later.

```ts
import { setTimeout as sleep } from "node:timers/promises";    // a sleep that understands signals

async function longJob(signal: AbortSignal): Promise<void> {
  try {
    await sleep(60_000, undefined, { signal });                 // rejects with an AbortError when aborted
  } catch (e) {
    if ((e as Error).name === "AbortError") {
      console.log("cleaning up");                               // release resources, then re-throw
    }
    throw e;
  }
}

const controller2 = new AbortController();
const job = longJob(controller2.signal);
setTimeout(() => controller2.abort(), 1000);                    // cancel after 1 s
try {
  await job;
} catch {
  console.log("cancelled");
}

// A worker loop that checks the signal between jobs
async function worker(jobs: AsyncIterable<number>, signal: AbortSignal): Promise<void> {
  for await (const job of jobs) {
    signal.throwIfAborted();                                    // stop between jobs
    await handle(job);
  }
}

// Outgoing HTTP call that obeys the signal
const res = await fetch("https://example.com/slow", { signal: AbortSignal.timeout(2000) });
// fs.readFile(path, { signal }), events.once(emitter, "x", { signal }), and timers/promises accept signals too
```

Synchronous code cannot be interrupted: a `for` loop with no `await` ignores the signal and blocks every other task. In a long computation, check `signal.aborted` between iterations and, in the main thread, give the event loop a chance to run (`await setImmediate()` from `node:timers/promises`) or move the work to a worker thread.

## Stopping Work When an HTTP Request Is Cancelled

When a client disconnects before the response is finished, the response object emits `"close"` without having finished writing. Turn that into an abort signal and pass it to every slow operation, and those operations stop on their own.

```ts
import { createServer } from "node:http";
import { setTimeout as sleep } from "node:timers/promises";

async function slowQuery(signal: AbortSignal): Promise<string> {
  await sleep(5000, undefined, { signal });                     // stands in for slow work
  return "done";
}

const server = createServer(async (req, res) => {
  const clientGone = new AbortController();
  res.on("close", () => {
    if (!res.writableFinished) clientGone.abort();              // the client disconnected early
  });
  const signal = AbortSignal.any([clientGone.signal, AbortSignal.timeout(2000)]);   // also enforce our own deadline

  try {
    const result = await slowQuery(signal);
    res.end(result);
  } catch (e) {
    if (clientGone.signal.aborted) return;                      // the client is gone: nobody to answer
    if (signal.aborted && (signal.reason as Error).name === "TimeoutError") {
      res.writeHead(504).end("timeout");
      return;
    }
    res.writeHead(500).end((e as Error).message);
  }
});
server.listen(8080);
```

A task that ignores its signal cannot be stopped and keeps running (and keeps its memory) after the request is over.

## Graceful Shutdown

```ts
const server = createServer(handler).listen(8080);

function shutdown(reason: string): void {
  console.log(`${reason} received, shutting down`);
  server.close(() => {                           // stop accepting connections; this callback runs when in-flight requests finish
    process.exit(0);
  });
  server.closeIdleConnections();                 // drop idle keep-alive connections so close() can complete
  setTimeout(() => process.exit(1), 10_000).unref();   // hard stop after 10 s; unref() so this timer alone does not keep Node alive
}

process.on("SIGINT", () => shutdown("SIGINT"));      // Ctrl+C
process.on("SIGTERM", () => shutdown("SIGTERM"));    // what Docker, Kubernetes and most process managers send
```

---

# Types, Interfaces and Classes in TypeScript

TypeScript models data with object types and interfaces, behavior with classes and functions, abstraction with interfaces, and code reuse with composition. Unlike Python, the types are checked by the compiler by their shape (structural typing) and are erased at run time. The Python OOP section below covers the class-based version of the same ideas.

## Object Types, Interfaces and Type Aliases

```ts
// An object type written inline, as a type alias, or as an interface
type Car = { brand: string; model: string };

interface Point {
  x: number;
  y: number;
  label?: string;                  // optional property: string | undefined
  readonly id: number;             // cannot be assigned after creation
}

const p: Point = { x: 1, y: 2, id: 7 };
// p.id = 8;                       // compile error: Cannot assign to 'id' because it is a read-only property
// p.z = 3;                        // compile error: Property 'z' does not exist on type 'Point'

interface Point3D extends Point { z: number }           // interfaces can extend each other

type ID = string | number;                              // a type alias can name any type, including unions
type Pair<T> = [T, T];                                  // ... and generics
```

* `interface` and `type` overlap: both describe object shapes. Interfaces can be extended and merged (declared twice to add properties); type aliases can also name unions, tuples, and function types. Pick one style and stay consistent.
* An object literal with a property the type does not mention is rejected (excess property check); a variable with extra properties is accepted (structural typing).
* Fields with no value set stay `undefined` (see Default Values); mark them optional (`?`) or give them a default.

## Classes

```ts
class Car {
  static wheels = 4;                                    // class attribute (shared by all cars)
  private odometer = 0;                                 // private to the class: checked by the compiler only
  #secret = "abc";                                      // # private: enforced at run time too

  constructor(public brand: string, public model: string) {}   // "parameter properties": declare AND assign in one go

  info(): string {                                      // method
    return `${this.brand} ${this.model}`;
  }

  get mileage(): number { return this.odometer; }       // getter: car.mileage reads like a field
  set mileage(value: number) {                          // setter with validation
    if (value < 0) throw new RangeError("mileage cannot be negative");
    this.odometer = value;
  }

  static create(brand: string): Car {                   // static method: called on the class, not an instance
    return new Car(brand, "unknown");
  }
}

const car = new Car("Toyota", "Corolla");
console.log(car.info());                                // Toyota Corolla
car.mileage = 1000;
Car.wheels;                                             // 4
Car.create("Honda");
// car.odometer;                                        // compile error: Property 'odometer' is private
```

```ts
// Inheritance and abstract classes
abstract class Shape {
  abstract area(): number;                              // subclasses must implement it
  describe(): string { return `area ${this.area().toFixed(2)}`; }
}

class Circle extends Shape {
  constructor(private radius: number) { super(); }      // super() must run before using this
  override area(): number { return Math.PI * this.radius ** 2; }
}

class Sedan extends Car {
  constructor(brand: string, model: string, public doors: number) {
    super(brand, model);
  }
  override info(): string {                             // "override" is checked: a typo becomes a compile error
    return `${super.info()} (${this.doors} doors)`;
  }
}

// new Shape();                                         // compile error: Cannot create an instance of an abstract class
```

## Methods and `this`

In a method, `this` is the object the method was called on. The catch is that `this` is decided by how a function is called, not where it was written. Pulling a method off its object loses `this`.

```ts
class Counter {
  n = 0;
  inc() { this.n++; }                       // regular method: `this` depends on the call
  incArrow = () => { this.n++; };           // arrow function property: `this` is fixed to the instance
}

const c = new Counter();
c.inc();                                    // fine: called as c.inc()

const detached = c.inc;
// detached();                              // TypeError: Cannot read properties of undefined (reading 'n')
const bound = c.inc.bind(c);                // bind fixes `this`
bound();
setTimeout(c.incArrow, 0);                  // safe: the arrow function keeps this
setTimeout(() => c.inc(), 0);               // also safe
```

Python's bound methods never lose `self`; in TypeScript, pass callbacks as arrow functions or bind them. The same issue is why a method passed to `setTimeout`, `addEventListener`, or `.map` can break.

## Composition

Prefer "has-a" over "is-a": store another object in a field and call it. TypeScript classes have single inheritance, so composition (and interfaces) is the normal way to combine behavior.

```ts
class Logger {
  constructor(private prefix: string) {}
  log(msg: string): void { console.log(this.prefix + msg); }
}

class Server {
  constructor(private logger: Logger, public addr: string) {}   // the dependency is passed in
  start(): void { this.logger.log(`starting on ${this.addr}`); }
}

new Server(new Logger("[srv] "), ":8080").start();
```

```ts
// Mixins: a function that takes a class and returns a class with extra behavior (a substitute for multiple inheritance)
type Constructor<T = {}> = new (...args: any[]) => T;

function Timestamped<TBase extends Constructor>(Base: TBase) {
  return class extends Base {
    createdAt = new Date();
  };
}

class User { constructor(public name: string) {} }
const TimestampedUser = Timestamped(User);
const u = new TimestampedUser("Alice");
u.createdAt;                                // Date
```

## Interfaces and `implements`

An interface is a set of property and method signatures. A class can promise to follow one with `implements`, and the compiler checks it. But because typing is structural, a plain object (or a class that never mentions the interface) fits as well if it has the right members. This is Python's duck typing, verified at compile time.

```ts
interface Notifier {
  send(message: string): void | Promise<void>;
}

class EmailNotifier implements Notifier {
  constructor(private to: string) {}
  send(message: string): void { console.log(`email to ${this.to}: ${message}`); }
}

class SlackNotifier {                                    // does not say "implements": still fits
  constructor(private channel: string) {}
  send(message: string): void { console.log(`slack ${this.channel}: ${message}`); }
}

const sms = { send(message: string) { console.log("sms:", message); } };   // a plain object fits too

function alert(n: Notifier, msg: string) {              // works with ANY value that has send(string)
  return n.send(msg);
}

alert(new EmailNotifier("ops@example.com"), "deploy done");
alert(new SlackNotifier("#ops"), "deploy done");
alert(sms, "deploy done");
```

Guidelines:

* Keep interfaces small (one to three members); define the interface where it is used (the consumer), not next to the implementation
* Ask for the narrowest type you need in a parameter (an interface, or `readonly number[]` instead of `number[]`), and return a concrete type
* `implements` is optional documentation with a compile-time check; `abstract class` is the choice when you also want shared code
* An interface exists only at compile time: `x instanceof Notifier` does not work. Use classes with `instanceof`, or check properties (`"send" in x`).

## unknown, any, Narrowing and Type Guards

`unknown` is the safe "anything" type: you cannot use the value until you have checked what it is. `any` switches checking off. Checking narrows the type inside the branch.

```ts
let x: unknown = "hello";

if (typeof x === "string") {
  x.toUpperCase();                      // x is string here
} else if (typeof x === "number") {
  x.toFixed(2);                         // x is number here
} else if (x instanceof Date) {
  x.getFullYear();
} else if (Array.isArray(x)) {
  x.length;                             // any[]
}

// A user-defined type guard: tells the compiler what a true result means
interface Cat { meow(): void }
interface Dog { bark(): void }
function isCat(pet: Cat | Dog): pet is Cat {
  return "meow" in pet;
}
function speak(pet: Cat | Dog) {
  if (isCat(pet)) pet.meow();           // narrowed to Cat
  else pet.bark();                      // narrowed to Dog
}

// Discriminated union: a shared literal field picks the variant (the TypeScript "type switch"; see Conditionals)
type Msg = { kind: "text"; body: string } | { kind: "image"; url: string };
function show(m: Msg) {
  switch (m.kind) {
    case "text": return m.body;
    case "image": return m.url;
  }
}

const n = x as number;                  // assertion: no check, the compiler just believes you
const config = { port: 8080 } satisfies Record<string, number>;   // checks the shape but keeps the precise type
```

## How TypeScript Differs from Python's Class-Based OOP

* Types are erased: there is no run-time record of an interface or type alias, only of classes (`instanceof`)
* Typing is structural, not nominal: a value fits a type if it has the right members, whatever it was declared as
* `private` is a compile-time rule; `#field` is truly private at run time; Python's `_name` and `__name` are conventions and name mangling
* Single inheritance only (`extends` one class); combine behavior with interfaces, composition, and mixins
* No operator overloading and no `__dunder__` methods; the protocols that do exist use `Symbol` keys (`Symbol.iterator`, `Symbol.asyncIterator`, `Symbol.dispose`) and `toString`/`toJSON`
* No method overloading at run time: overload signatures only describe the types of one implementation
* `get` and `set` replace `@property`; `static` replaces `@staticmethod` and `@classmethod`; `constructor` replaces `__init__`, and there is no `__del__` (use `try`/`finally` or `using`)
* `this` replaces `self`, but it is not passed automatically through a detached method (see above)

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

## Iteration in TypeScript

TypeScript uses the JavaScript iteration protocol, which matches Python's. An iterable has a method under the key `Symbol.iterator` that returns an iterator; an iterator has `next()` that returns `{ value, done }`. `for...of`, spread (`...`), destructuring, `Array.from`, `Map`, and `Set` all use it.

```ts
const nums = [1, 2, 3];                   // an iterable (an array is NOT an iterator)
const it = nums[Symbol.iterator]();       // get an iterator
it.next();                                // { value: 1, done: false }
it.next();                                // { value: 2, done: false }
it.next();                                // { value: 3, done: false }
it.next();                                // { value: undefined, done: true }   (no exception, unlike StopIteration)

[...it];                                  // []   the iterator is exhausted (it is also iterable, returning itself)

for (const [i, v] of ["a", "b"].entries()) {}   // index and value
for (const ch of "héllo") {}                    // strings iterate by code point
for (const [k, v] of new Map([["a", 1]])) {}    // Maps give [key, value] pairs

// A custom iterator, written by hand (the Counter example)
class Counter implements Iterable<number> {
  constructor(private limit: number) {}
  [Symbol.iterator](): Iterator<number> {
    let n = 0;
    const limit = this.limit;
    return {
      next: (): IteratorResult<number> =>
        n < limit ? { value: ++n, done: false } : { value: undefined, done: true },
    };
  }
}
for (const num of new Counter(3)) console.log(num);    // 1 2 3
```

### Generators and yield

A function declared with `function*` is a generator function. Calling it does not run the body; it returns a generator, which is an iterator. Each `next()` runs the body to the next `yield`, hands out that value, and pauses with the locals intact. The values are produced lazily.

```ts
function* countUp(limit: number): Generator<number> {
  console.log("start");
  let n = 1;
  while (n <= limit) {
    yield n;                               // pause here and give n to the caller
    n++;
  }
  console.log("finished");
}

const gen = countUp(2);                    // nothing printed yet
gen.next();                                // prints "start", returns { value: 1, done: false }
gen.next();                                // { value: 2, done: false }
gen.next();                                // prints "finished", returns { value: undefined, done: true }

// The easiest way to make a class iterable: a generator method
class Range implements Iterable<number> {
  constructor(private start: number, private end: number) {}
  *[Symbol.iterator](): Generator<number> {
    for (let i = this.start; i < this.end; i++) yield i;
  }
}
[...new Range(2, 5)];                      // [2, 3, 4]
```

```ts
// Infinite sequence: impossible with an array, trivial with a generator
function* fibonacci(): Generator<number> {
  let [a, b] = [0, 1];
  while (true) {
    yield a;
    [a, b] = [b, a + b];
  }
}

function* take<T>(iterable: Iterable<T>, n: number): Generator<T> {      // Python: itertools.islice
  if (n <= 0) return;
  let i = 0;
  for (const value of iterable) {
    yield value;
    if (++i >= n) return;
  }
}
[...take(fibonacci(), 10)];                // [0, 1, 1, 2, 3, 5, 8, 13, 21, 34]
// newer runtimes also have iterator helpers: fibonacci().take(10).toArray()

// Process a huge file without loading it: an ASYNC generator over lines
import { createReadStream } from "node:fs";
import { createInterface } from "node:readline";

async function* longLines(path: string): AsyncGenerator<string> {
  const rl = createInterface({ input: createReadStream(path, "utf-8") });
  for await (const line of rl) {
    if (line.length > 80) yield line;
  }
}
for await (const line of longLines("data.txt")) console.log(line);

// yield* delegates to another iterable
function* chainTwo<T>(a: Iterable<T>, b: Iterable<T>): Generator<T> {
  yield* a;
  yield* b;
}
```

* There are no generator expressions: use a generator function, or `map`/`filter` on arrays (which are eager and build a new array)
* A generator is single use; once consumed it is empty, so call the generator function again to start over
* `for await (... of ...)` and `async function*` combine iteration with `await` for data that arrives over time (streams, paginated APIs); arrays' own `map`/`filter`/`forEach` are not lazy and do not await
* Libraries such as `iter-tools` or the built-in iterator helpers (in newer runtimes) offer the `itertools` building blocks

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

## Generics in TypeScript

Generics are a core part of TypeScript, and they are enforced by the compiler (Python's are only hints). A type parameter goes in angle brackets, and the compiler usually infers it from the arguments.

```ts
// Generic function: T is inferred from the argument
function first<T>(items: T[]): T | undefined {
  return items[0];
}
first([1, 2, 3]);                   // number | undefined
first(["a", "b"]);                  // string | undefined

// A constraint limits which types are allowed (Python: bound / constrained TypeVar)
function double<T extends number | bigint>(x: T): T {
  return ((x as any) * (typeof x === "bigint" ? 2n : 2)) as T;
}

function longest<T extends { length: number }>(a: T, b: T): T {      // anything with a length: strings, arrays
  return a.length >= b.length ? a : b;
}
longest("abc", "de");               // "abc"
longest([1, 2], [1, 2, 3]);         // [1, 2, 3]

// Several type parameters, and a default
function pair<A, B = string>(a: A, b: B): [A, B] {
  return [a, b];
}

// Generic class
class Box<T> {
  constructor(private item: T) {}
  get(): T { return this.item; }
}
const intBox = new Box(123);        // Box<number>
const strBox = new Box("hello");    // Box<string>

// Generic interface and type alias
interface Repository<T extends { id: number }> {
  find(id: number): Promise<T | undefined>;
  save(item: T): Promise<void>;
}
type ApiResponse<T> = { data: T; error?: string };

// Generic stack
class Stack<T> {
  private items: T[] = [];
  push(item: T): void { this.items.push(item); }
  pop(): T | undefined { return this.items.pop(); }
}
const st = new Stack<number>();
st.push(1);
// st.push("a");                    // compile error: Argument of type 'string' is not assignable to parameter of type 'number'
```

```ts
// Generic helpers the standard library does not provide as one-liners
function mapValues<T, U>(items: T[], f: (item: T) => U): U[] {
  return items.map(f);
}

function groupBy<T, K extends string | number>(items: T[], keyOf: (item: T) => K): Record<K, T[]> {
  const out = {} as Record<K, T[]>;
  for (const item of items) {
    (out[keyOf(item)] ??= []).push(item);
  }
  return out;
}
groupBy(["apple", "avocado", "banana"], (s) => s[0]);   // { a: ["apple", "avocado"], b: ["banana"] }

// keyof and indexed access: type-safe property access
function getProp<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];
}
getProp({ name: "Alice", age: 30 }, "age");             // number
// getProp({ name: "Alice" }, "email");                 // compile error: '"email"' is not assignable to '"name"'

// Optional and union types (Python: Optional[str], Union[int, str])
function findUser(id: number): string | undefined {
  return id === 1 ? "Alice" : undefined;
}
function process(value: number | string): string {
  return String(value);
}
```

```ts
// Built-in utility types that transform other types
interface User { id: number; name: string; email?: string }

type Draft = Partial<User>;                 // every property optional
type Complete = Required<User>;             // every property required
type Preview = Pick<User, "id" | "name">;   // only these properties
type NoEmail = Omit<User, "email">;         // all except these
type Frozen = Readonly<User>;               // read-only properties
type Lookup = Record<string, User>;         // string keys, User values
type Result = ReturnType<typeof findUser>;  // string | undefined
type Resolved = Awaited<Promise<number>>;   // number
```

Key rule: generics document and enforce what type goes in and what comes out without hardcoding one type, and they disappear at run time like all types. Reach for generics when you write the same logic for several types; for a single type or a behavior difference, a plain function or an interface is simpler.

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

```ts
console.log({ value });         // prints the name and the value: { value: 42 }
console.table([{ a: 1 }, { a: 2 }]);
debugger;                       // pauses here when a debugger is attached
```

```bash
node --inspect-brk dist/app.js  # start paused and wait for a debugger (open chrome://inspect or attach VS Code)
node --enable-source-maps dist/app.js   # stack traces point at the .ts lines
```

IDEs (VS Code, PyCharm, WebStorm) provide the same with breakpoints you click. For TypeScript, make sure `"sourceMap": true` is set in `tsconfig.json` so the debugger can show your `.ts` source while running the compiled `.js`; VS Code's "JavaScript Debug Terminal" attaches to anything you start inside it.

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

```ts
// The built-in console has levels but no filtering, timestamps, or structure
console.debug("noisy detail");
console.info("server started on port", 8080);
console.warn("disk almost full");
console.error("payment failed for order", 123, error);   // goes to stderr, prints the stack for Error objects

// A logging library gives levels, timestamps, and JSON output for log collectors (pino shown)
import pino from "pino";

const logger = pino({ level: "info" });           // debug lines are hidden at this level
logger.info({ port: 8080 }, "server started");
logger.warn({ freeMb: 120 }, "disk almost full");
logger.error({ err, order: 123 }, "payment failed");
const child = logger.child({ module: "billing" });    // adds fields to every line
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

## Testing in TypeScript

TypeScript needs a test runner. Vitest and Jest are the most common (`npm install -D vitest`); Node.js also has a built-in runner (`node:test`). Test files end in `.test.ts` or `.spec.ts` and are discovered automatically.

```ts
// file: src/mathutils.ts
export function divide(a: number, b: number): number {
  if (b === 0) {
    throw new RangeError("division by zero");
  }
  return a / b;
}

// file: src/mathutils.test.ts
import { describe, it, expect } from "vitest";
import { divide } from "./mathutils.js";

describe("divide", () => {
  it("divides two numbers", () => {
    expect(divide(10, 2)).toBe(5);
  });

  it("throws on zero", () => {
    expect(() => divide(1, 0)).toThrow(RangeError);
  });

  // Table-driven test (Python: parametrize)
  it.each([
    [6, 3, 2],
    [9, 3, 3],
    [0, 5, 0],
  ])("divide(%i, %i) = %i", (a, b, expected) => {
    expect(divide(a, b)).toBe(expected);
  });
});

// Async code: return or await the promise
it("loads a user", async () => {
  await expect(getUser("octocat")).resolves.toMatchObject({ login: "octocat" });
});
```

```ts
// Replacing a dependency with a fake (mocking)
import { vi } from "vitest";
const send = vi.fn().mockResolvedValue(true);
await notify({ send }, "hello");
expect(send).toHaveBeenCalledWith("hello");

// The built-in runner needs no packages
import test from "node:test";
import assert from "node:assert/strict";
test("divide", () => {
  assert.equal(divide(10, 2), 5);
});
```

```bash
npx vitest                      # watch mode: reruns on every save
npx vitest run                  # run everything once (what CI uses)
npx vitest run -t divide        # only tests whose name matches
npx vitest run --coverage       # coverage report (needs @vitest/coverage-v8)
node --test                     # the built-in runner
```

## Formatting, Linting and Type Checking

* Python formatting: `black` or `ruff format`; style guide: PEP 8
* Python linting: `ruff` (fast, replaces flake8 and more) or `pylint`
* Python type checking: `mypy` or `pyright`
* TypeScript formatting: `prettier` (or Biome): it rewrites the file to one consistent style, so formatting is never debated
* TypeScript linting: `eslint` with `typescript-eslint` (finds bugs such as floating promises, unused variables, and unsafe `any`), or Biome
* TypeScript type checking: `tsc --noEmit` (part of the compiler; run it in CI, because dev servers and bundlers often skip it)

```bash
# Python
ruff format .
ruff check .
mypy src/

# TypeScript
npx prettier --write .
npx eslint .
npx tsc --noEmit
```

Run formatting, linting, type checks, and tests automatically in CI (and optionally in a pre-commit hook) so problems never reach the main branch.

## Everyday TypeScript Commands

```bash
npx tsc                  # type-check and compile to the outDir
npx tsc --noEmit         # only type-check (what CI runs)
npx tsc --watch          # recompile on save
npx tsx src/app.ts       # run a .ts file directly (no type-check)
node dist/app.js         # run the compiled output
npm install              # install dependencies from package.json and the lock file
npm ci                   # exact install from the lock file (CI, Docker)
npm install -D pkg       # add a dev dependency
npm run build            # run a script from package.json
npm test                 # run the "test" script
npx vitest run           # run the tests once
npx eslint .             # lint
npx prettier --write .   # format
npm outdated             # which dependencies have newer versions
npm audit                # known vulnerabilities in dependencies
node --version           # the runtime version
npx tsc --version        # the compiler version
```

## Measuring Performance

```python
import timeit
timeit.timeit("sum(range(1000))", number=10_000)      # time a snippet

import cProfile
cProfile.run("main()")                                  # which functions take the time
```

```ts
console.time("sum");                                    // time a snippet
let total = 0;
for (let i = 0; i < 1000; i++) total += i;
console.timeEnd("sum");                                 // sum: 0.03ms

const start = performance.now();                        // high-resolution timer in milliseconds
work();
console.log(`took ${(performance.now() - start).toFixed(2)} ms`);
```

```bash
python -m cProfile -s cumtime app.py                    # sort by cumulative time
node --cpu-prof dist/app.js                             # writes a .cpuprofile: open it in Chrome DevTools or VS Code
node --inspect dist/app.js                              # attach DevTools: Performance and Memory tabs
```

Benchmarks of tiny snippets are easy to get wrong (the JIT compiler warms up and optimizes away unused work), so use a library such as `tinybench` or `mitata` for micro-benchmarks. Measure first, then optimize the part that is actually slow; guessing is usually wrong.

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

# Python vs TypeScript at a Glance

```text
                    Python                          TypeScript
Typing              dynamic, strong                 static (compile time), structural, types erased at run time
Runs as             bytecode on the Python VM       JavaScript on V8 (Node.js, Deno, Bun, browsers)
Build step          none (compiles on import)       tsc / esbuild turns .ts into .js
Entry point         if __name__ == "__main__"       the file runs top to bottom (package.json "main" / scripts)
Null value          None                            undefined and null
Unset variable      NameError                       undefined (strict mode forces assignment before use)
Collections         list, tuple, dict, set          Array, tuple type, Object / Map, Set
Strings             Unicode text, immutable         UTF-16 code units, immutable, no char type
Numbers             int (unlimited), float          number (64-bit float), bigint
Equality            == value, is identity           === (objects by reference), no built-in deep compare
Errors              exceptions: try/except/finally  exceptions: try/catch/finally, throw, Promise rejections
Cleanup             with statement                  try/finally, using
Memory              reference counting + cyclic GC  generational tracing GC (V8), no pointers
Objects             classes, multiple inheritance   classes (single inheritance), interfaces, mixins
Interfaces          duck typing (runtime)           structural typing (compile time)
Concurrency         threads (GIL), processes,       one thread + event loop, async/await,
                    asyncio                         worker threads
Parallel CPU work   multiprocessing                 worker_threads, child processes
Cancellation        task.cancel(), Event            AbortController / AbortSignal
Dependencies        pip + venv + requirements /     npm + node_modules + package.json / package-lock.json
                    pyproject.toml
Tests               pytest / unittest               vitest / jest / node:test
Format              black / ruff format             prettier
Lint / type check   ruff, mypy / pyright            eslint, tsc --noEmit
```
