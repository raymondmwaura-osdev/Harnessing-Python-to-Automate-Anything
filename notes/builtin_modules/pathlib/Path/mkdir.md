# `Path.mkdir()`

## Sections

* [Overview](#overview)
* [Syntax, Parameters and Return Value](#syntax-parameters-and-return-value)
* [Errors and Exceptions](#errors-and-exceptions)
* [Examples](#examples)
* [Best Practices](#best-practices)
* [Common Pitfalls](#common-pitfalls)
* [Notes](#notes)

---

## Overview

* Creates a new directory at the filesystem path represented by the `Path` object.
* Enables structured directory creation within the object-oriented `pathlib` framework, facilitating cross-platform filesystem interactions.

---

## Syntax, Parameters and Return Value

**Syntax**:

```python
def mkdir(self, mode: int = 0o777, parents: bool = False, exist_ok: bool = False) -> None
```

**Parameters**:

* `mode`: `int`; file permission bitmask (e.g., `0o777`).
* `parents`: `bool`; if `True`, creates any missing parent directories; default is `False`.
* `exist_ok`: `bool`; if `True`, suppresses error when the target directory already exists; default is `False`.

**Return Value**:

* Returns `None`. Its primary effect is to perform filesystem I/O by creating the specified directory structure.

---

## Errors and Exceptions

* Raises `FileExistsError` if the target directory already exists and `exist_ok=False`.
* Raises `FileNotFoundError` if parent directories are missing and `parents=False`.
* May raise `PermissionError` when lacking necessary filesystem permissions (implied by general I/O behavior).

---

## Examples

```python
from pathlib import Path

# Simple directory creation
Path("data").mkdir()

# Create nested directories, even if parents don't exist
Path("data/nested/subdir").mkdir(parents=True)

# Suppress error if directory already exists
Path("data").mkdir(exist_ok=True)

# Full control
Path("proj/logs/archive").mkdir(parents=True, exist_ok=True)
```

---

## Best Practices

* Use `parents=True, exist_ok=True` when creating nested directories to avoid redundant error handling.
* Always work with explicit `Path` objects; not chaining on the `None` return value, e.g.:

```python
p = Path('some/path'); p.mkdir(...)
```

rather than: `p = Path('some/path').mkdir(...)`.

---

## Common Pitfalls

* Expecting `.mkdir()` to return a `Path` object; when in fact it returns `None`, causing attribute errors if chained directly.
* Missing parent directories without `parents=True`, triggering `FileNotFoundError`.
* Forgetting `exist_ok=True` when repeatedly creating the same directory; leading to `FileExistsError`.
* Overlooking `mode`: default permissions may be altered by the system’s `umask` or vary across operating systems.

---

## Notes

* Introduced in Python 3.4 with the `pathlib` module.
* `mode` behavior is platform-dependent; actual permissions consider the system’s `umask`.
* `Path.mkdir()` is generally preferred over legacy approaches (`os.mkdir()` or `os.makedirs()`) for its clarity, readability, and cross-platform consistency.

---
