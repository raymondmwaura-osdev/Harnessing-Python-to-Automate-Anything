# `parse_args()`

## Introduction

### What it does

When you call `parser.parse_args()`:

* It looks at the arguments (typically `sys.argv[1:]`, the command‐line arguments excluding the program name) by default.
* It matches them against the definitions you provided with `add_argument()`.
* It converts each argument string into the appropriate type (if you specified a `type=`).
* It applies certain actions (e.g., store, store_true, append) depending on how you've configured each argument.
* It produces a `Namespace` object containing attributes whose names correspond to the arguments (or flags) you defined. You then access them via dot notation (e.g., `args.foo`).
* If the user invokes your program with invalid arguments (missing required ones, wrong type, unknown option), `parse_args()` will print a usage message and exit (by default) with an error.

### Why and when you call it

You typically call `parse_args()` after you have finished defining all the arguments you expect. It is the **final step** in setting up argument parsing, before you move on to the business logic of your program. If you use `parse_args()` too early, you might not have defined all arguments yet.

---

## Basic Example

Here’s a minimal example to illustrate the use of `parse_args()`.

```python
import argparse

parser = argparse.ArgumentParser(description="Example program")
parser.add_argument("filename", help="Name of the file to process")
parser.add_argument("-v", "--verbose", action="store_true",
                    help="Enable verbose output")

args = parser.parse_args()

if args.verbose:
    print("Verbose mode enabled")
print("Processing file:", args.filename)
```

### Explanation

* `parser = argparse.ArgumentParser(...)` creates the parser.
* `add_argument("filename", ...)` defines a **positional** argument named `filename`.
* `add_argument("-v", "--verbose", action="store_true", ...)` defines an **optional** flag.
* `args = parser.parse_args()` parses what the user passed on the command line (for example `python program.py myfile.txt -v`).
* After that, `args.filename` holds the filename string, and `args.verbose` holds `True` or `False`.

If you run `python program.py -h`, the module automatically provides a help message including usage and description.

---
