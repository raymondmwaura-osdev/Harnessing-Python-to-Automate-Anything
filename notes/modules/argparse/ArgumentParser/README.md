# `ArgumentParser`

The `ArgumentParser` class is the principal entry point to the `argparse` library, which is part of Python’s standard library for handling command-line arguments. An instance of `ArgumentParser` serves as a container for argument definitions and the logic to interpret them.

---

## Major Methods of `ArgumentParser`

1. **`__init__(...)` (the constructor)**
   Initializes the parser. Accepts parameters such as:

   * `prog`: program name (default is script name),
   * `usage`: custom usage message,
   * `description` / `epilog`: text shown before/after help,
   * `parents`: allow argument inheritance from other parsers,
   * `formatter_class`: control help formatting,
   * `prefix_chars`, `argument_default`, `add_help`, `allow_abbrev`, etc.

2. **`add_argument(...)`**
   Defines both positional and optional arguments. You specify names or flags (e.g., `"filename"` or `"-v/--verbose"`) and behaviors such as `action`, `type`, `default`, `choices`, `nargs`, and more.

3. **`parse_args(args=None, namespace=None)`**
   Performs parsing. Converts input strings into properly typed attributes, assigns them into a `Namespace` object (or user-supplied `namespace`), and returns it. If `args` is `None`, it parses from `sys.argv`.

4. **`add_subparsers(...)`**
   Enables support for subcommands (e.g., `git commit`, `git push`). Calling this returns an object that can create sub-parsers via `add_parser()`. Use this to structure command-line tools with modular subcommands.

5. **`add_argument_group(...)`**
   Organizes arguments into help groups to improve readability of help output. Common for separating related options in complex CLIs.

---

## Minor Methods and Attributes

While the following are also part of the parser’s API, they are typically secondary in routine usage:

* `format_help()`
* `format_usage()`
* `print_help()`
* `print_usage()`
* `exit()`
* `error(message)`
* `set_defaults(**kwargs)`
* `register('action', ...)`

---
