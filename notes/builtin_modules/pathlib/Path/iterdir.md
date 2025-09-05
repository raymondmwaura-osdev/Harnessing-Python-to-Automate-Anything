# `Path.iterdir()`

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

`Path.iterdir()` enables iteration over the immediate contents of a directory, yielding `Path` objects for each entry. It is non-recursive, meaning it does not traverse subdirectories, and is commonly used to examine direct child files and folders within a given directory.

---

## Syntax, Parameters and Return Value

**Syntax**:

```python
def iterdir(self) -> Iterator[Path]
```

**Parameters**:

This method accepts **no parameters**.

**Return Value**:

* Returns an **iterator** that yields `Path` objects corresponding to each item (files, directories, other entries) in the specified directory.

---

## Errors and Exceptions

* Raises **`NotADirectoryError`** if the `Path` instance does not refer to a directory.
* May raise **`PermissionError`** if the process lacks sufficient rights to access the directory contents.
* Any other I/O-related errors (e.g., filesystem errors) will propagate as exceptions from underlying system calls.

---

## Examples

```python
from pathlib import Path

p = Path('.')
for entry in p.iterdir():
    print(entry)
```

This code will print each entry in the current directory; both files and subdirectories.

---

## Best Practices

* Use `Path.iterdir()` when you only need to explore a single level of a directory hierarchy; this is both expressive and efficient.
* Immediately filter the iterator, for example with `entry.is_file()` or `entry.is_dir()`, rather than building an intermediate list.

```python
from pathlib import Path

p = Path('.')
files_only = [e for e in p.iterdir() if e.is_file()]
```

* Combine with other `pathlib` methods for cleaner logic and better readability.

---

## Common Pitfalls

* Assuming recursive behavior: `iterdir()` does **not** descend into nested directories; use `Path.rglob()` or manual recursion if needed.
* Forgetting to confirm that the `Path` refers to a directory before calling `iterdir()`, resulting in unexpected exceptions.
* Materializing the iterator into a list unnecessarily when only iteration is needed; this can increase memory usage for large directories.

---

## Notes

* Introduced in Python **3.4** as part of the `pathlib` module.
* Platform-agnostic; works consistently across different operating systems.
* Helps simplify directory exploration, replacing older `os.listdir()` or `os.scandir()` patterns with a more Pythonic, object-oriented syntax.

---
