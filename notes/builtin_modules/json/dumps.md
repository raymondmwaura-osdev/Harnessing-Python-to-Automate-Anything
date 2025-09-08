# `json.dumps`

## Sections

* [Overview](#overview)
* [Syntax, Parameters, and Return Value](#syntax-parameters-and-return-value)
* [Errors and Exceptions](#errors-and-exceptions)
* [Examples](#examples)
* [Best Practices](#best-practices)
* [Common Pitfalls](#common-pitfalls)
* [Notes](#notes)

---

## Overview

The function `json.dumps()` (where "s" connotes "string") is an essential method within Python’s built-in `json` module, tasked with serializing Python objects (such as dictionaries, lists, strings, numbers, booleans, and `None`) into a JSON-formatted string. This is particularly valuable for preparing data for storage in text-based formats, network transmission, or embedding within other string-based contexts where direct file writing (via `json.dump()`) is not appropriate.

---

## Syntax, Parameters, and Return Value

### Syntax

```python
json.dumps(obj, skipkeys=False, ensure_ascii=True, check_circular=True,
           allow_nan=True, cls=None, indent=None, separators=None,
           default=None, sort_keys=False, **kw)
```

### Parameters

* **`obj`**: (Required) The Python object to serialize (e.g., list, dict).
* **`skipkeys`** (`bool`, default `False`): Skip non-basic-key dictionary items if `True`; otherwise raise `TypeError`.
* **`ensure_ascii`** (`bool`, default `True`): Escape non-ASCII characters when `True`; allow them as-is when `False`.
* **`check_circular`** (`bool`, default `True`): Detect and prevent circular references; disabled detection when `False`.
* **`allow_nan`** (`bool`, default `True`): Permit `NaN`, `Infinity`, `-Infinity` as JSON-equivalent tokens when `True`; raise `ValueError` otherwise.
* **`cls`**: A subclass of `JSONEncoder` to customize serialization logic.
* **`indent`** (`int` or `None`, default `None`): Pretty-print JSON with indentation if an integer is provided; compact string if `None`.
* **`separators`** (`tuple`, default `(', ', ': ')`): Customize item and key separators, enabling compact output when adjusted.
* **`default`**: A function invoked to convert non-serializable types to a JSON-compatible form.
* **`sort_keys`** (`bool`, default `False`): Sort dictionary keys alphabetically when `True`.

### Return Value

Returns a Python `str` representing the JSON-serialized form of the input object. Unlike `json.dump()`, it writes nothing to disk and produces the JSON string directly.

---

## Errors and Exceptions

* **`TypeError`**: Raised if dictionary keys are not basic types (e.g., tuple) and `skipkeys=False`.
* **`ValueError`**: Occurs when serializing non-finite floats (`NaN`, `Infinity`, `-Infinity`) with `allow_nan=False`.
* **Other TypeErrors**: May arise if `obj` contains values not serializable without supply of a `default`.
* **Errors in `default`**: Any exception raised within the `default` function will propagate, potentially `TypeError` or others depending on implementation.

---

## Examples

### 1. Basic Usage

```python
import json
data = {"name": "Alice", "age": 30, "is_student": False}
json_string = json.dumps(data)
print(json_string)
# Output: {"name": "Alice", "age": 30, "is_student": false}
```

Converts Python object into minimal JSON string.

### 2. Readability and Formatting

```python
json_pretty = json.dumps(data, indent=4, sort_keys=True)
print(json_pretty)
# Output:
# {
#     "age": 30,
#     "is_student": false,
#     "name": "Alice"
# }
```

Produces indented, sorted output.

### 3. Compact Output and Non-ASCII Handling

```python
data = {"message": "こんにちは", "count": 5}
compact = json.dumps(data, separators=(",", ":"), ensure_ascii=False)
print(compact)
# Output: {"message":"こんにちは","count":5}
```

Suppresses unnecessary spaces, preserves Unicode characters.

### 4. Serializing Non-Serializable Objects

```python
from datetime import datetime

def default_serializer(obj):
    if isinstance(obj, datetime):
        return obj.isoformat()
    raise TypeError(f"Type {type(obj)} not serializable")

data = {"timestamp": datetime.utcnow()}
json_ts = json.dumps(data, default=default_serializer)
print(json_ts)
```

Handles custom types via `default` function.

---

## Best Practices

* Use `indent` and `sort_keys` for readability in logs and human-facing files.
* Use compact output (`separators=(",", ":")`, `ensure_ascii=False`) for efficient network transmission or storage.
* Provide clear `default` functions or `cls` subclasses for non-standard types.
* Always catch relevant exceptions (`TypeError`, `ValueError`) when handling dynamic or external data.
* Choose `json.dumps()` for in-memory output and `json.dump()` when writing directly to files.

---

## Common Pitfalls

* Assuming tuple keys or unsupported types will serialize; leads to `TypeError`.
* Ignoring Unicode and inadvertently escaping non-ASCII characters if `ensure_ascii` is left as `True`.
* Misunderstanding that JSON does not preserve Python types like tuples; they convert to lists.
* Forgetting to handle special floats properly, resulting in `ValueError` unless `allow_nan=True` is set.
* Overloading `default` improperly and letting serialization fail silently or with unexpected errors.

---

## Notes

* **Custom `JSONEncoder` via `cls`**: For reusing serialization logic across contexts, subclassing `JSONEncoder` may be cleaner than repeating `default` logic each time.
* **YAML compatibility**: The default JSON output is also valid YAML 1.0/1.1, enabling dual-format readability.
* **Security Considerations**: While `dumps()` is generally safer than deserialization, be mindful when serializing untrusted or very large structures; it may still pose performance concerns.

---
