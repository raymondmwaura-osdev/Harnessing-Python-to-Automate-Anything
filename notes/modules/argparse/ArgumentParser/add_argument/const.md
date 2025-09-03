# `const`

> **Short Definition**: A fixed value that `argparse` assigns to an argument when certain conditions are met, such as with `store_const`, `append_const`, or when using `nargs='?'`.

---

## Key Topics

- [Overview](#overview)
- [Allowed Values](#allowed-values)
- [Examples](#examples)
- [Interaction With Other Parameters](#interaction-with-other-parameters)
- [Best Practices](#best-practices)
- [Common Pitfalls](#common-pitfalls)
- [Notes and References](#notes-and-references)

---

## Overview

The `const` parameter is used to assign a predetermined value in cases where the command-line argument itself doesn’t directly provide one.  
It exists to enable flag-like arguments that, when present, automatically set a fixed constant value.  
It is required in some `action` choices (e.g., `store_const`, `append_const`) and optional but powerful with `nargs='?'`, allowing optional values for arguments.  
Without `const`, certain actions would have no meaningful value to store when an argument is encountered.

---

## Allowed Values

The value of `const` can be *any Python object*: strings, numbers, types, booleans, functions, etc.  
Its meaning depends entirely on the `action` and `nargs` values. There are no restricted predefined keywords like there are with `nargs`.

---

## Examples

### Example 1: `store_const`

```python
import argparse
p = argparse.ArgumentParser()
p.add_argument('--debug', action='store_const', const='DEBUG', default='INFO')
args = p.parse_args()
print(args)
````

#### Output and Explanation

```bash
$ python script.py
Namespace(debug='INFO')

$ python script.py --debug
Namespace(debug='DEBUG')
```

* When `--debug` is present, `const='DEBUG'` is stored.
* When absent, the `default='INFO'` applies.

---

### Example 2: `append_const`

```python
import argparse
p = argparse.ArgumentParser()
p.add_argument('--use-int', dest='funcs', action='append_const', const=int)
p.add_argument('--use-str', dest='funcs', action='append_const', const=str)
args = p.parse_args()
print(args)
```

#### Output and Explanation

```bash
$ python script.py --use-int
Namespace(funcs=[<class 'int'>])

$ python script.py --use-int --use-str
Namespace(funcs=[<class 'int'>, <class 'str'>])
```

* Each flag appends its `const` value to the same list.
* This is handy for building a pipeline of operations.

---

### Example 3: `nargs='?'` with `const`

```python
import argparse
p = argparse.ArgumentParser()
p.add_argument('--level', nargs='?', const='DEFAULT', default='NONE')
args = p.parse_args()
print(args)
```

#### Output and Explanation

```bash
$ python script.py
Namespace(level='NONE')

$ python script.py --level
Namespace(level='DEFAULT')

$ python script.py --level HIGH
Namespace(level='HIGH')
```

* Omitted: `default` is applied.
* Present without value: `const` is applied.
* Present with value: the provided value overrides.

---

## Interaction With Other Parameters

* **`action`**:

  * `store_const` and `append_const` require `const`.
  * With other actions, `const` is usually ignored unless `nargs='?'`.
* **`default`**:

  * Used when the argument is absent.
  * `const` only applies if the argument is present without a value (`nargs='?'`) or when required by the action.
* **`type`**:

  * `const` is not type-checked or converted, unlike normal arguments.
* **`nargs`**:

  * Only `?` makes use of `const`. Other `nargs` values ignore it.

---

## Best Practices

* Use `store_const` or `append_const` when flags should trigger specific constant values.
* Use `nargs='?'` + `const` when you want optional arguments that can either:

  * take a user-provided value,
  * fall back to a known constant if no value is given,
  * or fall back to a default if omitted entirely.
* For Boolean flags, prefer `store_true` and `store_false` over `store_const=True/False` for clarity.

---

## Common Pitfalls

* Assuming `const` is a fallback like `default` → it only applies when the argument is present **without a value** or with `store_const`/`append_const`.
* Forgetting that `const` is required for certain actions (otherwise parser may error).
* Expecting `const` to undergo `type` conversion; it won’t. You must set it in the right type yourself.
* Trying to use `const` with unsupported `nargs` values (`*`, `+`, integers). It only works with `?`.

---

## Notes and References

* **Python official docs**: [argparse](https://docs.python.org/3/library/argparse.html)

---
