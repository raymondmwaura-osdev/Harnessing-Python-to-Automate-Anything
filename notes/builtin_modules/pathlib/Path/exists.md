# `Path.exists()`

## Sections

+ [Overview](#overview)  
+ [Syntax, Parameters and Return Value](#syntax-parameters-and-return-value)  
+ [Errors and Exceptions](#errors-and-exceptions)  
+ [Examples](#examples)  
+ [Best Practices](#best-practices)  
+ [Common Pitfalls](#common-pitfalls)

---

## Overview

`Path.exists()` returns a Boolean indicating whether the given path points to an existing file or directory. It provides a direct, readable alternative to the older `os.path.exists()` function.  

---

## Syntax, Parameters and Return Value

**Syntax**:

```python
def exists(self) -> bool
```

**Parameters**:

* None. This method does not take any arguments.

**Return Value**:

* Returns `True` if the path exists in the filesystem.
* Returns `False` if the path does not exist or is inaccessible for other reasons (e.g., broken symbolic link).
* The return value is of type `bool`.

---

## Errors and Exceptions

* `PermissionError`: May occur in rare cases if the process lacks permissions to access the path in question.
* `OSError`: May be raised by the underlying system call in abnormal cases (e.g., filesystem errors).

For typical use, `Path.exists()` rarely raises exceptions and instead returns `False` for most non-existent paths.

---

## Examples

```python
from pathlib import Path

# Basic usage
p = Path("example.txt")
if p.exists():
    print("File exists.")
else:
    print("File does not exist.")

# Checking a directory
d = Path("data")
print(d.exists())  # True if "data" directory is present

# With symbolic links
symlink = Path("shortcut")
print(symlink.exists())  # Depends on whether the target exists
```

---

## Best Practices

* Use `Path.exists()` for pre-checks before file operations such as deletion or opening in read mode.
* Prefer `Path.is_file()` or `Path.is_dir()` if you specifically need to distinguish between files and directories rather than just checking for existence.
* In concurrent environments (e.g., multi-threaded or multi-process programs), treat existence checks as advisory only. A file may exist during the check but be deleted before subsequent operations. Always combine with appropriate exception handling.
* For symbolic links, consider whether you want to check the existence of the link itself (`Path.exists()`) or whether the target it points to is valid (`Path.resolve(strict=True)`).

---

## Common Pitfalls

* **Race conditions**: Checking existence before acting on a file can lead to race conditions. For example, the file might exist during the check but be deleted before you open it. Always wrap file operations in try-except blocks.
* **Symbolic link confusion**: `Path.exists()` returns `False` for broken symlinks. If you need to check the link itself regardless of target validity, use `Path.is_symlink()`.
* **Permission restrictions**: In cases where the process lacks sufficient permissions, `exists()` may return `False` even if the file is present, potentially leading to misleading results.
* **Overusing existence checks**: In many cases, directly attempting the operation and handling exceptions (EAFP – Easier to Ask Forgiveness than Permission) is more Pythonic than checking existence in advance.

---
