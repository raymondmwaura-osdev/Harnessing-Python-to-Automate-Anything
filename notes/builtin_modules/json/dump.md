# `json.dump`

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

The function `json.dump()` is designed to serialize a native Python object (such as dictionaries or lists) into JSON format and write it directly to a file-like object. Common applications include storing configuration data, logging structured information, and preparing data for inter-process communication or API consumption.

---

## Syntax, Parameters, and Return Value

### Syntax

```python
json.dump(obj, fp, *, skipkeys=False, ensure_ascii=True,
          check_circular=True, allow_nan=True, cls=None,
          indent=None, separators=None, default=None,
          sort_keys=False, **kw)
```

### Parameters

* **`obj`**: Required. The Python object to be serialized (e.g., dict, list, str, number).
* **`fp`**: Required. A file-like object with a `.write()` method, where the JSON output will be written.

Optional keyword parameters for customization:

* **`skipkeys`** (`bool`; default `False`): If `True`, non-serializable dictionary keys (e.g., tuples) are ignored instead of raising `TypeError`.
* **`ensure_ascii`** (`bool`; default `True`): If `True`, all non-ASCII characters are escaped; if `False`, characters are written as-is, preserving UTF-8.
* **`check_circular`** (`bool`; default `True`): If `True`, circular references in data structures are detected; otherwise, serialization may recurse infinitely.
* **`allow_nan`** (`bool`; default `True`): If `True`, allows serialization of special float values (`NaN`, `Infinity`, `-Infinity`) to their JavaScript equivalents; otherwise, raises `ValueError`.
* **`cls`**: Specifies a subclass of `JSONEncoder` for customized serialization.
* **`indent`** (`int` or `None`; default `None`): Specifies the number of spaces for indentation to produce human-readable JSON; `None` outputs compact JSON.
* **`separators`** (`tuple`; default `(', ', ': ')`): Defines item and key separators to control formatting.
* **`default`**: A function that returns a serializable representation for objects unsupported by default (used internally in `JSONEncoder`).
* **`sort_keys`** (`bool`; default `False`): If `True`, dictionary keys are sorted in the output JSON.

### Return Value

`json.dump()` writes to the provided file object and returns `None`. It does not produce a string result; rather, its effect is side-effectual; writing the JSON directly to `fp`.

---

## Errors and Exceptions

* **`TypeError`**: Raised when encountering dictionary keys not of allowed types (e.g., tuples), unless `skipkeys=True` is specified.
* **`ValueError`**: Resulting from disallowed special float values (`NaN`, `Infinity`, `-Infinity`) when `allow_nan=False`.
* **File-related exceptions**: `FileNotFoundError`, `IOError`, and other I/O errors may occur when the file cannot be opened or written.
* **`OverflowError`** or other custom exceptions: May occur if the provided `default` function fails to serialize an unsupported type.

These exceptions alert developers to serialization constraints, invalid data types, or I/O anomalies, thereby enhancing robustness.

---

## Examples

1. **Basic Usage**

```python
import json

data = {"name": "John", "age": 30, "city": "New York"}
with open("output.json", "w", encoding="utf-8") as f:
    json.dump(data, f)
```

This writes compact JSON to `output.json`.

2. **Pretty-Printing with Indentation and Sorted Keys**

```python
import json

data = {"city": "New York", "age": 30, "name": "John"}
with open("pretty.json", "w", encoding="utf-8") as f:
    json.dump(data, f, indent=4, sort_keys=True)
```

Produces readable, alphabetically sorted JSON.

3. **Skipping Non-Serializable Keys & Handling Non-ASCII Characters**

```python
import json

data = {("tuple",): "value", "greeting": "¡Hola Mundo!"}
with open("data.json", "w", encoding="utf-8") as f:
    json.dump(data, f, skipkeys=True, ensure_ascii=False, indent=2)
```

The tuple key is omitted; special characters are preserved.

---

## Best Practices

* **Use `encoding="utf-8"`** when opening files to ensure consistent handling of non-ASCII characters, as recommended by JSON standards.
* **Choose `dump()` vs. `dumps()` appropriately**: use `dump()` for files, `dumps()` when working with in-memory strings.
* **Employ `indent`, `sort_keys`, and `ensure_ascii=False`** for improved readability or debugging.
* **Guard custom serialization via `default` or `cls`** for user-defined types.
* **Wrap in `try...except`**, handling `TypeError`, `ValueError`, and I/O errors gracefully.

---

## Common Pitfalls

* **Confusing `dump()` with `dumps()`**: Passing a filename string to `dump()` instead of a file object triggers `TypeError`.
* **Ignoring encoding**: Failure to set encoding may cause silent corruption of non-ASCII content.
* **Data fidelity misassumptions**: JSON cannot represent Python-specific types like tuples directly.
* **Circular references**: Without disabling `check_circular`, data structures with self-references may cause infinite recursion.
* **Unhandled special floats**: Without `allow_nan=True`, serializing `NaN` or `Infinity` raises `ValueError`.

---

## Notes

* **Security caution**: While less risky than deserialization, writing JSON from untrusted or massive data structures may still incur performance costs; especially if used to build APIs or logs dynamically.
* **Advanced customization**: The `cls` parameter facilitates subclassing `JSONEncoder` to extend serialization logic; for instance, serializing datetime objects or complex classes.
* **Alternatives**: The third-party library `simplejson` offers a nearly identical API with potential performance enhancements and broader feature support.

---
