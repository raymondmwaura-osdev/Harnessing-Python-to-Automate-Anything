# `pathlib.Path.read_bytes`

## Overview

The `read_bytes` method belonging to the `Path` class in the pathlib module provides a convenient way to read the entire contents of a file as a bytes object. It opens the file in binary mode, reads all its contents into memory, closes the file, and returns the data. Typical use-cases include reading non-text files (images, executables, audio data) or when you explicitly need raw bytes rather than decoded text. It interacts naturally with other `Path` methods (such as `exists()`, `is_file()`, `open()`) and represents a more object-oriented alternative to `open(filename, "rb").read()`.

---

## Syntax

```python
Path.read_bytes()
```

Note: No parameters are passed to `read_bytes`.

---

## Parameters

There are **no parameters** for this method besides the implicit `self` (the `Path` instance).

---

## Return Value

* **Type:** `bytes`
* **Description:** Returns a `bytes` object containing the entire contents of the file the `Path` instance refers to.
* **Special Notes:** Because the entire file is read into memory, this may be inefficient or impractical for very large files. There is no streaming or iterator behaviour: you get all the bytes at once.

---

## Errors and Exceptions

* **`FileNotFoundError`**; Raised if the `Path` does not exist or the file cannot be found.

  * *Example:*

    ```python
    from pathlib import Path
    p = Path("nonexistent.bin")
    p.read_bytes()  # raises FileNotFoundError
    ```
* **`PermissionError`**; Raised if the file exists but the process lacks permissions to read it.
* **`IsADirectoryError`**; Raised if the `Path` refers to a directory rather than a regular file.
* **`OSError` (or subclass)**; Other I/O errors may be raised (e.g., disk error, file locked).
* **MemoryError**; If the file is extremely large and cannot fit in memory; while not guaranteed, reading everything at once may cause memory exhaustion.

---

## Example

```python
from pathlib import Path

# Example 1: read a small binary file
file_path = Path("image.png")
data = file_path.read_bytes()
print(type(data))  # <class 'bytes'>
print(data[:10])   # e.g. b'\x89PNG\r\n\x1a\n\x00\x00'

# Example 2: using read_bytes for hashing
import hashlib
file_path = Path("document.pdf")
data = file_path.read_bytes()
digest = hashlib.sha256(data).hexdigest()
print("SHA-256:", digest)

# Example 3: error handling
file_path = Path("missing_file.dat")
try:
    data = file_path.read_bytes()
except FileNotFoundError:
    print("File was not found.")
```

---

## Best Practices

* Use `read_bytes()` when you need the entire file in memory as a bytes object and you know its size is safe to load.
* For very large files, prefer streaming via `Path.open("rb")` and reading in chunks to avoid high memory usage.
* Use `exists()` or `is_file()` on the `Path` before calling `read_bytes()`, if you wish to check presence explicitly (though catching exceptions also works).
* Recognize that this method reads raw bytes; if you need text decoding (e.g., UTF-8), use `read_text()` instead.

---

## Common Pitfalls

* Assuming `read_bytes()` is efficient for large files: it reads the whole file into memory, which can cause performance or memory issues.
* Confusing `read_bytes()` with `read_text()`: if you expect a string but call `read_bytes()`, you’ll get a `bytes` object and may need to decode.
* Using it on a directory path: this will raise `IsADirectoryError` instead of returning something meaningful.
* Relying on `read_bytes()` when you only need a small portion of the file: in such cases streaming reads or `open()` with slices may be better.
* Ignoring exception handling: because file I/O always carries risk of missing files, permissions, etc., you should handle or propagate exceptions deliberately.

---
