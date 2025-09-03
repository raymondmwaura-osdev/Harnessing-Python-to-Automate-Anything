# `parameter_name`

## Key Topics

- [Overview](#overview)
- [Allowed Values](#allowed-values)
    <!-- Replace "Value 1" and "Value 2" with the actual values like `'?'` in the 'nargs' parameter. -->
    - [Value 1](#value-1)
    - [Value 2](#value-2)
    ...
    <!--- You can have as many values as the parameter allows. -->
- [Examples](#examples)
- [Interaction With Other Parameters](#interaction-with-other-parameters)
- [Best Practices](#best-practices)
- [Common Pitfalls](#common-pitfalls)

---

## Overview

+ What this parameter does
+ Why it exists / what problem it solves
+ General behavior and quirks

---

## Allowed Values

Break down the full range of accepted inputs.
If the value of the parameter is anything that the user wants, like the `const` parameter, state and explain that and move on. Skip "Value 1" and "Value 2"

### Value 1 (e.g., `?` for `nargs`)
+ **Meaning**: What it represents.
+ **Behavior**: How argparse treats it.
+ **Special Cases**: Optional defaults, empty lists, or constants.

#### Example

```python
import argparse
p = argparse.ArgumentParser()
# your example here
```

#### Output and Explanation

```bash
$ python script.py args...
Namespace(...)
```

Explanation of why the output looks like that.

---

### Value 2

(same structure as value 1)

---

## Examples

Show 2–3 “summary” examples that demonstrate the most *practical* uses of the parameter. These can cross-reference multiple values.

---

## Interaction With Other Parameters

Explain how this parameter plays with:

* `default`
* `const`
* `type`
* `action`
* others, depending on relevance

---

## Best Practices

* How to use the parameter efficiently
* When to choose one value over another
* Tips for clarity and maintainability

---

## Common Pitfalls

* Mistakes users often make
* Subtle differences (e.g., `nargs=1` vs `nargs=None`)
* Edge cases that trigger errors or confusion

---