# `help`

## Key Topics

* [Overview](#overview)
* [Allowed Values](#allowed-values)

  * [String or `argparse.SUPPRESS`](#string-or-argparse-suppress)
* [Examples](#examples)
* [Interaction With Other Parameters](#interaction-with-other-parameters)
* [Best Practices](#best-practices)
* [Common Pitfalls](#common-pitfalls)

---

## Overview

The `help` parameter in `add_argument()` provides a textual description of the argument for inclusion in auto-generated usage and help ("-h"/"--help") messages. This parameter enhances the usability of command-line interfaces by conveying the purpose and expected input for each argument. The `help` text is rendered when users invoke help flags, guiding them without consulting external documentation. Additionally, if `help=argparse.SUPPRESS`, the argument is excluded from help output, enabling selective hiding of advanced or internal options.

---

## Allowed Values

### String

* A string describes the purpose of the argument, displayed in the help output.
* The string value is rendered next to the argument in help output. It may include format specifiers (e.g., `%(default)s`, `%(prog)s`) to dynamically incoroprate relevant values.

### `argparse.SUPPRESS`

* The special constant `argparse.SUPPRESS` indicates that the argument should not appear in the help output.
* `argparse.SUPPRESS` causes exclusion of the argument from help display, though it remains functional.
* Useful for hiding advanced flags such as debug or version flags, without exposing them in normal usage documentation.

#### Example

```python
import argparse
from argparse import SUPPRESS

parser = argparse.ArgumentParser()
parser.add_argument('--verbose', action='store_true', help='Enable detailed output')
parser.add_argument('--secret', help=SUPPRESS)
args = parser.parse_args()
print(args)
```

#### Output and Explanation

```bash
$ python script.py -h
usage: script.py [-h] [--verbose]

optional arguments:
  -h, --help   show this help message and exit
  --verbose    Enable detailed output
```

The `--secret` argument is functional but remains absent from the help output, as intended by `SUPPRESS`.

---

## Examples

1. **Standard descriptive help**

   ```python
   parser.add_argument('--input', help='Path to input file')
   ```

   This clearly conveys the expected usage to users.

2. **Default value interpolation**

   ```python
   parser.add_argument('--timeout', type=int, default=30,
                       help='Timeout in seconds (default: %(default)s)')
   ```

   Inserts the default value into the help text: "Timeout in seconds (default: 30)".

3. **Hiding internal flags**

   ```python
   parser.add_argument('--debug-mode', action='store_true',
                       help=argparse.SUPPRESS)
   ```

   Retains functionality while concealing it from standard help output.

---

## Interaction With Other Parameters

* **`default`**: When included in help strings via `%(default)s`, default values are interpolated at help rendering time.
* **`metavar`, `choices`, `type`**: These may be referenced within the help string using `%()` specifiers for clarity and context.
* **`formatter_class`**: Defined at the parser level, it affects how help text is formatted (e.g. indentation, wrapping). `help` strings may be formatted differently based on the selected formatter.
* **`add_help=False`**: Disables the automatic `-h`/`--help` flag; custom help action may be added using the `help` parameter.

---

## Best Practices

* Provide concise but informative help messages that succinctly describe each argument's purpose.
* Use format specifiers (`%(default)s`, etc.) for dynamic content inclusion.
* Use `argparse.SUPPRESS` sparingly for advanced or rarely used flags to streamline help output.
* Consistently document arguments with `help`, avoiding undocumented options where possible, to improve UX and maintainability.

---

## Common Pitfalls

* **Absence of help text**: Omitting `help` results in arguments appearing without explanation, diminishing usability.
* **Invalid format specifiers**: Mistyped or unsupported placeholders (e.g., `%(defaut)s`) lead to format errors.
* **Unexpected suppression**: Applying `SUPPRESS` unintentionally hides important flags. Always verify help output.
* **Formatting inconsistencies**: Long help strings may be poorly wrapped unless a suitable `formatter_class` is used for readability.

---
