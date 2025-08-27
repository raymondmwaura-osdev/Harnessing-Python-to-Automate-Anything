# `FUNCTION_NAME`

## Key Topics

- [Overview](#overview)
- [Syntax](#syntax)
- [Parameters](#parameters)
- [Return Value](#return-value)
- [Errors and Exceptions](#errors-and-exceptions)
- [Examples](#examples)
- [Practical Use Cases](#practical-use-cases)
- [Best Practices](#best-practices)
- [Common Pitfalls](#common-pitfalls)
- [Notes and References](#notes-and-references)

---

## Overview

Explain what the function does, why it exists, and its conceptual purpose within the `shutil` module. Clarify whether it deals with files, directories, archiving, or system queries.

---

## Syntax

```python
shutil.function_name(param1, param2, ...)
```

* Describe what the syntax looks like.
* Clarify optional vs required parameters.

---

## Parameters

* **param1**: Type, meaning, role, constraints.
* **param2**: Type, meaning, role, constraints.
* Note any default values or platform-dependent behavior.

---

## Return Value

* Describe the type of return (e.g., string path, None, tuple).
* Mention if the function returns `dst` path for chaining, or no return value at all.

---

## Errors and Exceptions

* List possible exceptions raised (e.g., `FileNotFoundError`, `PermissionError`, `SameFileError`, `OSError`).
* Clarify when they occur.

---

## Examples

Provide 2-4 progressively complex examples:

1. Minimal usage (simple case).
2. Slightly advanced case (optional args).
3. Realistic workflow case (integration in a script).

---

## Practical Use Cases

Explain contexts where the function is valuable, such as:

* Backup scripts
* Automated testing environments
* System administration tasks

---

## Best Practices

* Offer safety tips, such as checking disk space before copying.
* Highlight efficiency strategies.
* Note when to prefer this function over another similar one.

---

## Common Pitfalls

* Misunderstandings about metadata preservation.
* Wrong assumptions about cross-filesystem behavior.
* Danger of destructive operations (e.g., overwriting files).

---

## Notes and References

* Mention quirks, e.g., differences between POSIX and Windows.
* Provide links to Python’s official documentation or other authoritative references.

---
