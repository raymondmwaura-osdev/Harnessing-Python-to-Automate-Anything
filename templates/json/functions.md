# `json.FUNCTION`

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

Provide a concise description of what the function does, its purpose in the `json` module, and the typical context in which it is used.
Example: *`json.dumps` serializes a Python object into a JSON-formatted string, enabling data exchange and storage in a standardized format.*

---

## Syntax, Parameters, and Return Value

### Syntax

```python
json.function_name(parameters...)
```

### Parameters

* **param\_name**: Explain its role, type, and default value if applicable.
* Cover optional keyword arguments (like `indent`, `sort_keys`, `cls`, etc.).

### Return Value

* Specify the return type (e.g., string, Python object, `None`).
* Clarify if the result is written to a file (for `dump`) or returned directly (for `dumps`).

---

## Errors and Exceptions

* **TypeError**: Raised when an unsupported Python type is passed for serialization.
* **ValueError**: Raised for invalid JSON during deserialization.
* Add other relevant exceptions depending on the function (e.g., file handling errors for `dump`/`load`).
* Explain why each exception might occur in real usage.

---

## Examples

Provide \~3 examples of increasing complexity:

1. **Basic usage** with minimal parameters.
2. **Customized usage** with parameters like `indent` or `sort_keys`.
3. **Realistic usage** in a file operation or API-style workflow.

---

## Best Practices

* Highlight efficient and clear usage patterns.
* Mention readability improvements (e.g., using `indent` for logs or configs).
* State when to prefer one function over another (`dump` vs `dumps`, `load` vs `loads`).

---

## Common Pitfalls

* Misunderstanding of data type conversions (e.g., tuple → list).
* Forgetting to handle encoding (e.g., UTF-8 issues in files).
* Using `load` or `dump` incorrectly with strings instead of file objects.
* Overlooking that dictionary keys must be strings.

---

## Notes

* Mention advanced parameters (`cls`, `object_hook`, etc.) if not covered in the core sections.
* Reference the related encoder/decoder classes (`JSONEncoder`, `JSONDecoder`).
* Provide additional context (e.g., security considerations when parsing untrusted JSON).

---
