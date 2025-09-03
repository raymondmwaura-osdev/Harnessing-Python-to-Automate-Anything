# `required`

## Key Topics

* [Overview](#overview)
* [Allowed Values](#allowed-values)

  * [Boolean (`True` or `False`)](#boolean-true-or-false)
* [Examples](#examples)
* [Interaction With Other Parameters](#interaction-with-other-parameters)
* [Best Practices](#best-practices)
* [Common Pitfalls](#common-pitfalls)

---

## Overview

The `required` parameter indicates whether an argument must be supplied by the user. By default, optional arguments (those that include flags such as `-f` or `--foo`) are not mandatory. Setting `required=True` forces users to provide the argument, and the parser raises an error if it is omitted. However, using `required=True` for optional-style arguments is often considered counterintuitive, as users typically expect flags with dashes to be optional.

---

## Allowed Values

### Boolean (`True` or `False`)

* A boolean indicating whether the argument is mandatory (`True`) or optional (`False`).
* `required=False` (the default for optional arguments) allows omission; if omitted, the attribute defaults to `None` or the supplied `default` value.
* `required=True` forces presence; omission results in an error citing “the following arguments are required.”
* Positional arguments (those without dashes) are inherently required (unless controlled via `nargs`). Thus, `required` only applies meaningfully to flagged (optional) arguments.

#### Example

```python
import argparse
parser = argparse.ArgumentParser()
parser.add_argument('--radius', type=int, required=True, help='Radius of the circle')
args = parser.parse_args()
print(args)
```

#### Output and Explanation

```bash
$ python script.py --radius 5
Namespace(radius=5)
```

When `--radius` is supplied, parsing succeeds. Omitting it produces an error:

```bash
$ python script.py
error: the following arguments are required: --radius
```

This behavior demonstrates enforcement of mandatory input for flagged arguments.

---

## Examples

1. **Mandatory optional flag**

   ```python
   parser.add_argument('-u', '--username', required=True, help='User name')
   ```

   Ensures that the user must specify a username, otherwise the parser signals an error.

2. **Optional flag with default**

   ```python
   parser.add_argument('--log-level', choices=['debug', 'info', 'warning'], default='info', required=False)
   ```

   Exemplifies a typical optional argument; the user may omit it, and a default value is provided.

3. **Better organization of required flags in help output**

   ```python
   parser = argparse.ArgumentParser()
   group = parser.add_argument_group('required named arguments')
   group.add_argument('-i', '--input', required=True, help='Input file name')
   ```

   This grouping provides cleaner help formatting, as required options still appear under “optional arguments” by default.

---

## Interaction With Other Parameters

* **`default`**: When `required=False`, the provided `default` value is used if the argument is omitted. If `required=True`, the `default` is irrelevant because omission triggers an error.
* **`type`**: Applies conversion after checking that the argument is present (for required args) or supplied (for optional args).
* **`action`**: The `required` parameter does not affect `action`; they operate orthogonally. An argument can still perform a specific action whether it is required or not.
* **`nargs`**: If `nargs` determines multiple values are required, and the user omits the argument while `required=True`, the parser reports the mandatory requirement.
* **Positional arguments**: Do not utilize `required`; their necessity is inherent unless `nargs` allows optional presence (e.g. `nargs='?'`).

---

## Best Practices

* Avoid marking optional flags as required unless the semantic clarity justifies it. Flags traditionally signal optionality, so misusing them can confuse users.
* Prefer positional arguments for truly mandatory inputs; `argparse` will treat them as required by default.
* If you must require an optional argument, group them under a “required named arguments” section in help output for clarity.
* Always include clear help messages indicating purpose and requirement status.

---

## Common Pitfalls

* **Flagged arguments marked as required**: Users may not anticipate that flags require a value, leading to confusion.
* **Mismatch between default and required**: Setting both `required=True` and providing a `default` is contradictory; if default exists, the user is not forced to supply a value.
* **Help message confusion**: By default, required flags still appear under “optional arguments”, potentially misleading users. Grouping can mitigate this.
* **Incorrect assumption about positional arguments**: Developers may mistakenly set `required` on positional args; it's ignored as positional arguments default to required status.

---
