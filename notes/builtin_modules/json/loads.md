# `json.loads`

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

The function `json.loads()` is responsible for the transformation (deserialization) of JSON-formatted strings or byte sequences into equivalent native Python objects; typically dictionaries, lists, strings, numbers, booleans, or `None`. Its utility is particularly evident when handling JSON data received as strings, such as API responses or file contents read into memory.

The name `loads` suggests its purpose: "load string", meaning it accepts a JSON string and returns a Python object representation.

---

## Syntax, Parameters, and Return Value

### Syntax

```python
json.loads(s, *, cls=None, object_hook=None,
           parse_float=None, parse_int=None,
           parse_constant=None, object_pairs_hook=None, **kw)
```

### Parameters

* **`s`**: The required input. A JSON document in the form of a `str`, `bytes`, or `bytearray`.
* **`cls` (optional)**: A subclass of `JSONDecoder` to customize parsing behavior.
* **`object_hook` (optional)**: A function that transforms each decoded JSON object (dict) before it is returned.
* **`parse_float`**, **`parse_int`**, **`parse_constant`** (optional): Functions to customize conversion of JSON floats, integers, and constants like `NaN`, `Infinity`, `-Infinity`, respectively.
* **`object_pairs_hook` (optional)**: A function receiving an ordered list of pairs for each JSON object; allows preservation or transformation of ordering. This parameter takes precedence over `object_hook` if both are supplied.

### Return Value

* Returns a native Python object corresponding to the JSON input: dictionaries for objects, lists for arrays, and appropriate types for strings, numbers, booleans, or null values.

---

## Errors and Exceptions

* **`json.JSONDecodeError`**: Raised when the input string is not well-formed JSON (e.g., improper quotes, trailing commas, invalid characters, etc.).
* **`ValueError`**: In some contexts or older Python versions, malformed input may raise a `ValueError` rather than the more specific `JSONDecodeError`.
* **Type-related errors**: If the initial parameter `s` is not string-like or bytes-like, a `TypeError` or `AttributeError` may occur due to invalid input type.

---

## Examples

1. **Basic Usage**

```python
import json

s = '{"language": "Python", "version": 3.11}'
data = json.loads(s)
print(data)          # {'language': 'Python', 'version': 3.11}
print(type(data))    # <class 'dict'>
```

This demonstrates fundamental string-to-dictionary conversion.

2. **Nested Data Structures**

```python
import json

s = '{"A": {"X": 1, "Y": 1.0}, "B": [true, false, null]}'
data = json.loads(s)
print(data)          # {'A': {'X': 1, 'Y': 1.0}, 'B': [True, False, None]}
```

Illustrates handling of nested objects, arrays, and JSON constants like `true`, `false`, `null` mapping to Python equivalents.

3. **Advanced Parsing with Hooks**

```python
import json
from collections import OrderedDict

s = '{"id": 12, "name": "Alice", "scores": [85.5, 92.0]}'
data = json.loads(
    s,
    object_pairs_hook=OrderedDict,
    parse_float=lambda x: round(float(x), 2)
)
print(data)           # OrderedDict([...])
# floats in scores rounded to two decimal places
```

Demonstrates usage of `object_pairs_hook` and `parse_float` for custom behavior.

---

## Best Practices

* **Prefer `json.loads()` when working with in-memory strings**, and use `json.load()` when reading directly from file-like objects; this enhances code clarity and intent.
* **Wrap calls in `try...except`**, protecting against `json.JSONDecodeError` or `ValueError` to allow graceful error handling.
* **Utilize conversion hooks** (e.g., `object_hook`, `parse_*`) when you need Python-native or domain-specific objects.
* **Validate JSON format before parsing**, especially if the input comes from untrusted sources.

---

## Common Pitfalls

* **Invalid JSON formatting**, such as use of single quotes or trailing commas, leads to parsing failure.
* **Assuming tuple retention**: JSON arrays always become lists in Python; original tuple types are lost upon deserialization.
* **Incorrect input type**: Passing file handles or other non-string types to `loads()` may raise unexpected errors. Use `load()` for files.
* **Case sensitivity in JSON literals**: JSON expects `true`, `false`, `null` in lowercase. Using uppercase (`True`, etc.) will trigger errors.

---

## Notes

* The module supports non-standard JSON constants (`NaN`, `Infinity`, `-Infinity`) by mapping them to corresponding Python `float` values; though these are not part of the official JSON specification.
* **Security awareness**: Be cautious when parsing JSON from untrusted sources, especially very large or deeply nested strings. They may cause performance degradation or denial-of-service scenarios.
* The `cls` parameter allows specifying a custom `JSONDecoder` subclass for advanced parsing needs.
* For performance-critical applications or legacy compatibility, consider external alternatives such as `simplejson`, which maintains a similar API.

---
