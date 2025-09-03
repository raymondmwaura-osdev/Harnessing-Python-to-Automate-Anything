# `choices`

## Key Topics

* [Overview](#overview)
* [Allowed Values](#allowed-values)

  * [Any iterable of values](#any-iterable-of-values)
* [Examples](#examples)
* [Interaction With Other Parameters](#interaction-with-other-parameters)
* [Best Practices](#best-practices)
* [Common Pitfalls](#common-pitfalls)

---

## Overview

The `choices` parameter restricts the set of permissible values for a command-line argument. When provided, only values contained in the specified collection are accepted. If the user supplies a value not in this collection, `argparse` raises a validation error with a descriptive message. This mechanism enforces both user guidance and input validity at the parsing stage.

---

## Allowed Values

The `choices` parameter accepts:

### Any iterable of values

* A container (e.g., list, tuple, set) enumerating all acceptable values for the argument.
* During parsing, `argparse` ensures the provided argument matches one element from this container. If no match exists, it rejects the input with an appropriate error.
* This parameter can be used with both positional and optional arguments. The container may include strings, numbers, or even complex types, provided equality comparison works correctly.

#### Example

```python
import argparse
parser = argparse.ArgumentParser()
parser.add_argument('--format', choices=['json', 'xml'])
args = parser.parse_args()
print(args)
```

#### Output and Explanation

```bash
$ python script.py --format json
Namespace(format='json')
```

The argument `'json'` is accepted because it is among the specified choices. If the user provided an unsupported value, `argparse` would issue an error indicating the invalid choice and listing the valid options.

---

## Examples

1. **Restricting optional output formats**

   ```python
   parser.add_argument('--format', choices=['json', 'xml'], default='json')
   ```

   Ensures output format is limited to JSON or XML, defaulting to JSON if not specified.

2. **Language selection for processing**

   ```python
   parser.add_argument('language', choices=['en', 'de', 'es', 'pt'], help='Logging language')
   ```

   Validates that the language code is among predefined options.

3. **Operational mode enforcement**

   ```python
   parser.add_argument('--mode', choices=['backup', 'restore', 'delete'], required=True)
   ```

   Enforces selection of a valid operational mode; rejects anything else.

---

## Interaction With Other Parameters

* **`default`**: If `default` is set to a value not in `choices`, the parser issues an error only when parsing (not at definition). If default is valid, it is accepted without issue.
* **`type`**: `type` conversion occurs prior to validating against `choices`. This permits numeric or custom-object matching.
* **`nargs`**: When accepting multiple values (e.g., `nargs='+'`), each value is individually checked against `choices`.
* **`action`**: The validation happens before the `action` is applied; thereby ensuring that subsequent handling only operates on validated values.

---

## Best Practices

* Use `choices` when input must strictly belong to a known set; this guarantees early and clear user feedback on invalid entries.
* Favor human-readable, minimal choice lists to preserve clarity in help messages.
* Combine `choices` with `default` to provide both guidance and fallback behavior in a single configuration line.
* Apply `choices` with `type` to enforce both type correctness and domain constraints.

---

## Common Pitfalls

* **Mismatch between `default` and `choices`**; A default outside of `choices` may slip through until runtime, causing unexpected rejection or behavior.
* **Ambiguity in help output**; Long choice lists can clutter help text; consider documentation clarity.
* **Limitation to single choice**; By default, only one value is allowed. To accept multiple values while still applying `choices`, combine with `nargs` (e.g., `nargs='+'`), but remember each value is independently validated.
* **Typographical errors**; Mistyped input values will result in error messages of the form: “invalid choice: '…' (choose from '…')”, directing users accurately.

---
