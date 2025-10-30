# `parse_args()`

The `parse_args()` method is the mechanism by which an ArgumentParser object takes command-line argument strings and converts them into a structured object (a `Namespace`) that the program can inspect. Specifically:

* When called with no arguments, `parse_args()` examines the list of command-line arguments (by default `sys.argv[1:]`) and matches them against the definitions of acceptable options and positional arguments that have previously been defined via calls to `add_argument()`.
* It converts the individual string tokens to Python values of the appropriate type if a `type=` converter was specified.
* It assigns values to attributes on a namespace object; each defined argument leads to a corresponding attribute on the returned namespace.
* It handles optional arguments (flags) and positional arguments, default values, and enforces constraints such as `choices`, `required`, `nargs`, etc. If the user’s input does not satisfy the definitions (for instance, a required positional argument missing, wrong type, or invalid choice), `parse_args()` will typically emit a usage message and terminate (by default) with an error.
* Optionally, one can pass a custom list of argument strings to `parse_args(args=…)` instead of relying on the command-line; this allows programmatic invocation or testing of the parser.

---

## Parameters

The signature of the method in typical usage is something like:

```python
namespace = parser.parse_args(args=None, namespace=None)
```

+ `args`: *(optional)* A list of strings to parse instead of the default command-line arguments. If omitted or `None`, the parser uses `sys.argv[1:]` as the source of argument strings.
+ `namespace: *(optional)* An existing namespace object (or other object) into which the parsed attributes will be populated. If omitted or `None`, a new `argparse.Namespace` object is used as the container for parsed values.
+ If `namespace` is provided, then parsed values will be stored as attributes on that object, rather than creating a new namespace. This allows populating an existing object with parsed values.

The method returns the namespace object (either the one passed in via `namespace` or a new one) containing attributes whose names correspond to the defined `dest` values of arguments.

---

## Examples

### Example 1: Basic positional argument

```python
import argparse

parser = argparse.ArgumentParser(description="Echo a message.")
parser.add_argument("message", help="A message to echo")

args = parser.parse_args()
print("You said:", args.message)
```

* If run as `python script.py Hello`, output: `You said: Hello`.
* If omitted message, the parser will display usage and error out.

### Example 2: Optional flag and default value

```python
import argparse

parser = argparse.ArgumentParser(description="Greeter")
parser.add_argument("-n", "--name", default="World", help="Name to greet")
parser.add_argument("--verbose", action="store_true", help="Enable verbose mode")

args = parser.parse_args()
if args.verbose:
    print("Verbose mode ON")
print(f"Hello, {args.name}!")
```

* Running `python script.py` → `Hello, World!`
* Running `python script.py -n Alice` → `Hello, Alice!`
* Running `python script.py -n Alice --verbose` → `Verbose mode ON` then `Hello, Alice!`

### Example 3: Passing custom argument list

```python
import argparse

parser = argparse.ArgumentParser(description="Sum integers")
parser.add_argument("nums", metavar="N", type=int, nargs="+",
                    help="One or more integers to sum")

# Instead of reading from the real command line, supply custom list:
args = parser.parse_args(["3", "5", "7"])
print("Sum is", sum(args.nums))
```

* This prints `Sum is 15`.
* Using the `args=` parameter of `parse_args()` allows testing of parsing logic without relying on actual command-line invocation.

---
