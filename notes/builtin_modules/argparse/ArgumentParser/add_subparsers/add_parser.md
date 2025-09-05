# `add_parser`

## Key Topics

+ [Definition](#definition)
+ [Return Value](#return-value)
+ [Parameters](#parameters)
+ [Example](#example)

---

## Definition

The `add_parser(...)` method is provided by the object returned from `ArgumentParser.add_subparsers()`, which is an instance of the internal class `argparse._SubParsersAction`. The `add_parser` method is the only publicly documented interface offered by this action. It defines a subcommand by returning a **new `ArgumentParser` instance**, uniquely configured for that subcommand. This allows each subcommand to possess its own argument specification and help text.

---

## Return Value

The `add_parser(...)` method returns a fresh instance of `ArgumentParser`. This sub-parser is a fully functional argument parser that can define its own arguments using `.add_argument(...)`, and its invocation generates its own usage and help message, scoped to the given subcommand.

---

## Parameters

The signature of `add_parser` accepts:

* **`name`** *(str)*; the identifier for the subcommand, as typed on the command line.
* **`**kwargs`**; keyword arguments identical to those accepted by the `ArgumentParser` constructor, enabling customization of the sub-parser.

Typical parameters supplied include:

* **`help`** (*str*); brief description displayed in the list of subcommands.
* **`description`** (*str*); full description shown in the subcommand’s own help output.
* Other constructor parameters from `ArgumentParser`, such as:

  * `prog`; override the displayed program name for this subcommand.
  * `formatter_class`; controls help text formatting.
  * `add_help`, `parents`, `prefix_chars`, etc.

Advanced users may replace the class type using `parser_class` via `add_subparsers`, but `add_parser` itself merely forwards any given kwargs to construct its `ArgumentParser`.

---

## Example

```python
import argparse

# Create top-level parser
parser = argparse.ArgumentParser(prog="mytool", description="Main tool.")

# Enable subcommands
subparsers = parser.add_subparsers(dest="cmd", required=True,
                                   help="Available commands", metavar="CMD")

# Define 'build' subcommand
build_parser = subparsers.add_parser(
    "build",
    help="Compile the project",
    description="Compile project sources into binaries.",
    prog="mytool build"
)
build_parser.add_argument("--optimize", action="store_true", help="Enable optimization")

# Define 'clean' subcommand
clean_parser = subparsers.add_parser(
    "clean",
    help="Remove build artifacts",
    description="Clean all compiled files.",
)
clean_parser.add_argument("--deep", action="store_true", help="Remove dependencies as well")

args = parser.parse_args()
if args.cmd == "build":
    print(f"Building. Optimize: {args.optimize}")
elif args.cmd == "clean":
    print(f"Cleaning. Deep: {args.deep}")
```

* The `add_parser("build", ...)` call creates a new `ArgumentParser` specifically for the `build` subcommand.
* Sub-parser arguments and help are isolated and separate from the main parser.
* Each sub-parser accepts standard constructor parameters to adjust help formatting, description, program name, etc.

---
