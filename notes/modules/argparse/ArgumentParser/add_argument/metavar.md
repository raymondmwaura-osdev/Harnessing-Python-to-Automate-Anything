# `metavar`

## Key Topics

* [Overview](#overview)
* [Allowed Values](#allowed-values)

  * [String](#string)
  * [Tuple of strings (for multiple values)](#tuple-of-strings)
* [Examples](#examples)
* [Interaction With Other Parameters](#interaction-with-other-parameters)
* [Best Practices](#best-practices)
* [Common Pitfalls](#common-pitfalls)

---

## Overview

The `metavar` parameter specifies the name used to represent an argument’s value in help and usage messages. It replaces the default internal placeholder (inferred from `dest` or flag) with a clearer, more descriptive token. This enhances the readability and communicative clarity of generated command-line interface documentation. When combined with parameters such as `nargs`, `metavar` can accept multiple placeholders via a tuple.

---

## Allowed Values

### String

* A single token representing the argument’s value in usage/help messages.
* Displays this token instead of the default placeholder derived from the argument name.
* Particularly valuable for optional arguments with flags (e.g., `--input INPUT_PATH`), enhancing clarity.

#### Example

```python
import argparse
parser = argparse.ArgumentParser()
parser.add_argument('bar', metavar='XXX')
parser.add_argument('--foo', metavar='YYY')
parser.parse_args('X --foo Y'.split())
parser.print_help()
```

#### Output and Explanation

```bash
usage: [-h] [--foo YYY] XXX

positional arguments:
  XXX

optional arguments:
  -h, --help  show this help message and exit
  --foo YYY
```

The `metavar` customizes the placeholders: positional argument displays as `XXX`, optional `--foo` displays as `YYY`.

---

### Tuple of strings (for multiple values)

* A tuple specifying separate placeholders for each value when the argument consumes multiple inputs (`nargs` > 1).
* Each element of the tuple replaces the default placeholder for its corresponding argument instance in help output.
* Useful when `nargs` indicates two or more values, and descriptive placeholders are required for each.

#### Example

```python
import argparse
parser = argparse.ArgumentParser()
parser.add_argument('-p', '--param', nargs=2, metavar=('key', 'value'),
                    help='key and value for query parameters')
parser.print_help()
```

#### Output and Explanation

```bash
usage: [-h] [-p key value]

optional arguments:
  -h, --help    show this help message and exit
  -p key value  key and value for query parameters
```

Each placeholder (`key`, `value`) appears in order, conveying meaning more precisely than default.

---

## Examples

1. **Custom placeholder for input path**

```python
parser.add_argument('--input', metavar='INPUT_FILE', help='Input file path')
```

Enhances readability: `--input INPUT_FILE`.

2. **Multiple named placeholders with `nargs`**

```python
parser.add_argument('--coords', nargs=2, metavar=('LAT', 'LON'), help='Latitude and Longitude')
```

Displays: `--coords LAT LON`.

3. **Combined with positional argument clarity**

```python
parser.add_argument('output', metavar='DEST', help='Destination path')
```

Leads to usage segment: `DEST` for clarity.

---

## Interaction With Other Parameters

* **`nargs`**: If the argument accepts multiple values, `metavar` can be a tuple providing a placeholder for each. If a single value or `nargs=None`, a string suffices.
* **`help`** and **`usage`**: The `metavar` placeholder appears within auto-generated help/usage strings, ensuring uniform documentation.
* **`dest`**: `metavar` only affects display; it does not alter the attribute name used in the `Namespace` object.
* **`formatter_class`**: Impacts how `metavar` is laid out (e.g., wrapping, padding), but not the placeholder content itself.

---

## Best Practices

* Use clear, descriptive tokens (e.g., `INPUT_FILE`, `OUTPUT_DIR`) to improve user comprehension.
* Deploy a tuple of placeholders when parsing multiple values to maintain explicit usage messages.
* Avoid generic placeholders like `ARG`; prefer semantically meaningful names.
* Employ `metavar` consistently across both optional and positional arguments for uniformity in help output.

---

## Common Pitfalls

* **Empty `metavar`**: Setting it to an empty string (`''`) may lead to formatting issues in help output.
* **Tuple length mismatch**: When `nargs` requires multiple values, providing a `metavar` tuple of incorrect length produces inaccurate or confusing help messages.
* **Neglecting usage readability**: Omitting `metavar` leads to default placeholders derived from internal names, which may be less clear (e.g., `--foo FOO` vs `--foo DESIRED_PATH`).
* **Confusion with `dest`**: Developers may assume `metavar` influences variable naming in `Namespace`; it does not; its scope is purely display-oriented.

---
