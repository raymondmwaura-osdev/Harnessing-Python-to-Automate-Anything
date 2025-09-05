# `Path.is_dir()`

## Sections

+ [Overview](#overview)
+ [Syntax, Parameters and Return Value](#syntax-parameters-and-return-value)
+ [Errors and Exceptions](#errors-and-exceptions)
+ [Examples](#examples)
+ [Best Practices](#best-practices)
+ [Common Pitfalls](#common-pitfalls)
+ [Notes](#notes)

---

## Overview

`Path.is_dir()` determines whether the given path refers to an existing directory. This method plays a critical role in path querying within the broader context of `Path`, enabling developers to discriminate between files, directories, and nonexistent paths in a clear, object-oriented manner.

---

## Syntax, Parameters and Return Value

**Syntax**:

```python
Path.is_dir(self) -> bool
```

*Note:* `is_dir()` does not accept any arguments (e.g., `follow_symlinks`); it follows symlinks by default.

**Return Value**:

* Returns `bool`:

  * `True` if the path exists and is a directory,
  * `False` otherwise (including if the path does not exist or is a file).

---

## Errors and Exceptions

`Path.is_dir()` generally does not raise exceptions. If an I/O error occurs during the check (e.g., due to permission issues), the method will return `False` rather than raising `PermissionError` or similar exceptions. It handles missing or inaccessible paths gracefully by returning `False` rather than throwing an error.

---

## Examples

```python
from pathlib import Path

# Existing directory
p_dir = Path('/usr')
print(p_dir.is_dir())  # Expected: True

# Existing file
p_file = Path('/usr/bin/python3')
print(p_file.is_dir())  # Expected: False

# Nonexistent path
p_none = Path('/some/nonexistent/path')
print(p_none.is_dir())  # Expected: False
```

Iterating over entries in a directory:

```python
from pathlib import Path

p = Path('.')
for entry in p.iterdir():
    if entry.is_dir():
        print("Subdirectory:", entry.name)
```

This pattern is commonly used to filter directory entries.

---

## Best Practices

* Use `is_dir()` when you must differentiate directories from files or non-existent entries; especially in filesystem traversal or validation logic.
* Combine with other `Path` methods, for instance:

  ```python
  if p_dir.exists() and p_dir.is_dir():
      # Safe to proceed with directory-specific operations
  ```
* Utilize `iterdir()` with `is_dir()` for concise directory iteration:

  ```python
  [x for x in Path('.').iterdir() if x.is_dir()]
  ```

---

## Common Pitfalls

* **Symlink behavior**: `is_dir()` follows symbolic links by default. There is currently no `follow_symlinks` parameter to change this behavior.
* **Nonexistent paths**: It returns `False` without indicating whether the path doesn’t exist or simply isn’t a directory; checking `.exists()` separately can clarify intent.
* **Trailing slashes**: The presence or absence of trailing separators (e.g., `"dir/"`) does not affect `Path.is_dir()` results.
* **Permission issues**: If access is restricted, the method returns `False`, which may mask security-related failures.

## Notes

* **Version introduction**: `Path.is_dir()` was introduced when the `pathlib` module was added in Python 3.4.
* **Symlink handling enhancement request**: A proposed feature in Python 3.13 aims to allow specifying `follow_symlinks=False`, but as of now, this feature is not implemented.
* **Cross-platform consistency**: Behavior is consistent across Unix-like systems and Windows.
* **Complementary methods**: Related methods include `.is_file()`, `.exists()`, and `.is_symlink()`; together, they provide comprehensive path status querying.

---
