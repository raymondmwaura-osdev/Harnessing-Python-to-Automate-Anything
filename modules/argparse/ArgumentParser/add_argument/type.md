# `type`

## Key Topics

* [Overview](#overview)
* [Allowed Values](#allowed-values)

  * [Any callable](#any-callable)
* [Examples](#examples)
* [Interaction With Other Parameters](#interaction-with-other-parameters)
* [Best Practices](#best-practices)
* [Common Pitfalls](#common-pitfalls)

---

## Overview

The `type` parameter in `ArgumentParser.add_argument()` specifies a callable that converts the string input received from the command line into the desired Python object. By default, arguments are read as strings. The `type` parameter allows developers to enforce conversion into more specific types (e.g., integers, floats, or user-defined objects).

This parameter exists to simplify input validation and conversion at the parsing stage, ensuring arguments are stored in the correct form without requiring additional post-processing. If the conversion fails, `argparse` raises an error and provides a descriptive message to the user.

---

## Allowed Values

### Any callable

* Any Python callable that accepts a single string argument and returns a converted value.
* `argparse` applies the callable during parsing. If the callable raises a `ValueError` or `TypeError`, the parser raises `ArgumentTypeError` and terminates with an error message.
* Commonly used callables include built-in constructors (`int`, `float`, `str`) and custom validation functions.

#### Example

```python
import argparse

def comma_to_int_list(s: str):
    try:
        return [int(x) for x in s.split(',')]
    except ValueError as e:
        raise argparse.ArgumentTypeError(f"Invalid integer in list: {e}")

parser = argparse.ArgumentParser()
parser.add_argument('--numbers', type=comma_to_int_list)
args = parser.parse_args()
print(args.numbers)
```

#### Output and Explanation

```bash
$ python script.py --numbers 10,20,30
Namespace(numbers=[10, 20, 30])
```

The argument `10,20,30` is passed as a string. The custom callable converts it into a list of integers before storage in the `Namespace`.

---

## Examples

1. **Basic numeric conversion**

```python
parser.add_argument('--count', type=int)
```

This ensures the argument is stored as an integer.

2. **Custom validation**

```python
def positive_int(x):
    x = int(x)
    if x <= 0:
        raise argparse.ArgumentTypeError("Value must be positive")
    return x

parser.add_argument('--age', type=positive_int)
```

This enforces that the argument is both an integer and strictly positive.

3. **File path validation**

```python
import pathlib
parser.add_argument('--path', type=pathlib.Path)
```

This converts the input into a `Path` object, enabling immediate use with the `pathlib` API.

---

## Interaction With Other Parameters

* **`default`**: If a default value is a string, `type` is applied to it. If it is already of the correct type, it is stored as-is.
* **`const`**: The value of `const` is not transformed by `type`; it is stored exactly as provided.
* **`nargs`**: When combined with `nargs`, the `type` callable is applied to each element individually.
* **`action`**: The `type` conversion occurs before the specified `action` is executed. Thus, the `action` operates on the converted value.

---

## Best Practices

* Use built-in callables such as `int`, `float`, or `pathlib.Path` whenever possible for clarity and reliability.
* When defining custom callables, always raise `argparse.ArgumentTypeError` on invalid input to ensure consistent error handling.
* Keep the logic within the `type` callable focused on conversion rather than complex validation. More extensive validation is better handled after parsing.
* Define reusable type functions for common patterns (e.g., lists of integers, ranges of values).

---

## Common Pitfalls

* Passing a callable that does not accept exactly one string argument will result in errors during parsing.
* Confusion regarding `default`: a string default is converted by `type`, whereas a non-string default (such as an integer) is used directly.
* Using overly complex validation logic within `type` can reduce code readability and make error messages less clear.
* When used with `nargs='*'` or `nargs='+'`, developers must remember that `type` applies to each individual argument string rather than the group as a whole.

---
