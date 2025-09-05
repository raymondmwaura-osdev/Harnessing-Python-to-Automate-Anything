# `add_subparsers`

## Key Topics

+ [Definition](#definition)
+ [Return Value](#return-value)
+ [Parameters](#parameters)
+ [Example](#example)

---

## Definition

The `add_subparsers()` method of the `argparse.ArgumentParser` class facilitates the definition of **subcommands**; distinct modes of operation that a command-line program may support. When invoked, it prepares the parser to accept different subcommands (e.g., `git commit` vs. `git push`) by returning a subparsers controller. Each subcommand can then have its own arguments defined independently.

---

## Return Value

Invoking `add_subparsers()` yields a special **subparsers object**, representing an instance of the private action class typically named `_SubParsersAction`. This object exposes a key method, `add_parser(name, ...)`, which allows you to define and customize each subcommand’s parser.

---

## Parameters

`add_subparsers()` accepts several optional keyword arguments that influence the structure and help output of subcommands.

1. **`title`** (*str*)

   * Provides a heading under which all subcommands appear in the help message.

2. **`description`** (*str*)

   * Adds descriptive text beneath the `title` in the help output, clarifying the subcommands group.

3. **`prog`** (*str*)

   * Overrides the displayed program name for help messages of subcommands; otherwise, the main parser’s name is used.

4. **`parser_class`** (*type*)

   * Allows specification of a custom `ArgumentParser` subclass for generating each subcommand’s parser.

5. **`action`** (*str*)

   * Sets the action handling class name. Altering this parameter is rare. Passing it incorrectly may cause unexpected errors (e.g., clashes with other parameters such as `parser_class`).

6. **`dest`** (*str*)

   * Determines the attribute name under which the chosen subcommand’s name is stored in the result namespace (for example, `args.subcommand_name`).

7. **`required`** (*bool*, added in Python 3.7+)

   * If set to `True`, the user must specify one of the defined subcommands; otherwise, the parser will produce an error when no subcommand is supplied.

8. **`help`** (*str*)

   * Adds a brief summary for the group of subcommands, typically shown in the main help output.

9. **`metavar`** (*str*)

   * Alters the placeholder text for the subcommand name in the usage and help messages.

---

## Example

```python
import argparse

parser = argparse.ArgumentParser(prog="tool", description="Main tool entry point.")

subparsers = parser.add_subparsers(
    title="commands",
    description="Valid subcommands",
    dest="command",
    required=True,
    metavar="CMD",
    help="Choose one of these commands"
)

# Define subcommand 'init'
init = subparsers.add_parser(
    "init",
    help="Initialize the project",
    description="Perform project initialization"
)
init.add_argument("--force", action="store_true", help="Force re-initialization")

# Define subcommand 'status'
status = subparsers.add_parser(
    "status",
    help="Show status"
)
# ... add more arguments as needed

args = parser.parse_args()
print(f"Selected command: {args.command}")
```

* Using `required=True` ensures the user cannot omit a subcommand.
* The `dest="command"` parameter allows you to inspect which subcommand was invoked.
* The `title`, `description`, `help`, and `metavar` parameters affect how the help output is structured and presented.

---
