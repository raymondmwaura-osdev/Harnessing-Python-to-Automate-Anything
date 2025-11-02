# `pathlib.Path.write_text`

## Overview

The `write_text` method of the `Path` class in the `pathlib` module provides a mechanism for writing string data to a file. It opens the target file in text mode (- writing, not appending), writes the complete supplied string (after any encoding), then closes the file. This method is most useful when you want to overwrite a file (or create it if it doesn’t exist) with a full text content, and you already have the content as a Python `str`. It complements other `Path` methods such as `read_text()` (for reading) and `write_bytes()` (for binary data).

---

## Syntax

```python
Path.write_text(text, encoding=None, errors=None)
```

---

## Parameters

* **text**

  * **Type:** `str`
  * **Description:** The full text content to be written into the file. Existing content (if any) will be overwritten.
  * **Default Value:** None (required parameter)
  * **Constraints:** Must be a string. If the `Path` refers to a directory, or writing is not permitted, an error will be raised.

* **encoding**

  * **Type:** `str` (e.g., `'utf-8'`) or `None`
  * **Description:** The encoding to use when writing the text. If `None`, the system default encoding is used (which typically is `'utf-8'` on modern Python installations).
  * **Default Value:** `None`
  * **Constraints:** If encoding is specified, the string must be encodable under that codec; otherwise, a `UnicodeEncodeError` may occur.

* **errors**

  * **Type:** `str` (e.g., `'strict'`, `'ignore'`, `'replace'`) or `None`
  * **Description:** The error-handling scheme to use for encoding errors. If `None`, the default is `'strict'`.
  * **Default Value:** `None`
  * **Constraints:** Only relevant if encoding step encounters characters not representable in the chosen encoding.

---

## Return Value

* **Type:** `int`
* **Description:** Returns the number of characters (not bytes) written to the file.
* **Special Notes:** Because `write_text()` writes and closes the file in one action, it is not a streaming interface. If the write fails mid-process (e.g., due to full disk) the behaviour may vary (see Errors and Exceptions).

---

## Errors and Exceptions

* **`IsADirectoryError`**

  * **Cause:** The `Path` refers to a directory rather than a regular file when attempting write.
  * **Example:**

    ```python
    from pathlib import Path
    p = Path("my_directory")
    p.write_text("hello")  # raises IsADirectoryError
    ```

* **`PermissionError`**

  * **Cause:** The process lacks permission to write to the target file or directory.
  * **Example:** Attempting to write to a system-protected directory.

* **`OSError` / `IOError`**

  * **Cause:** Other I/O issues (e.g., disk full, filesystem read-only, network mount issue). For example, if the disk is full the file may end up zero bytes.
  * **Example:**

    ```python
    from pathlib import Path
    p = Path("/mnt/full_disk/file.txt")
    p.write_text("data")  # may write zero bytes or raise OSError depending on system
    ```

* **`UnicodeEncodeError`**

  * **Cause:** The text contains characters not encodable with the chosen `encoding`.
  * **Example:**

    ```python
    from pathlib import Path
    p = Path("file.txt")
    p.write_text("café", encoding="ascii")  # raises UnicodeEncodeError
    ```

---

## Example

```python
from pathlib import Path

# Example 1: Basic write (overwrites or creates)
p = Path("sample.txt")
chars_written = p.write_text("Hello, world!\nThis is a file.")
print(chars_written)  # e.g. 27 (number of chars)

# Example 2: Specifying encoding
p2 = Path("unicode.txt")
p2.write_text("Привет мир", encoding="utf-8")
print(p2.read_text(encoding="utf-8"))  # 'Привет мир'

# Example 3: Error handling
p3 = Path("readonly_dir/output.txt")
try:
    p3.write_text("Data")
except PermissionError:
    print(f"Cannot write to {p3}")
```

---

## Best Practices

* Use `write_text()` when you have the **complete text content** ready and you intend to **overwrite or create** the file in one go.
* For **very large files** or when you want to **append** rather than overwrite, consider using `Path.open(mode='w' or 'a', encoding=…)` with a context manager instead of `write_text()`.
* Always specify `encoding` explicitly when working with non-ASCII text to avoid ambiguity and platform-dependent defaults.
* Check for disk capacity or use atomic write patterns when data integrity matters (since `write_text()` may leave a zero-byte file if the disk fills mid-write).
* Use `exists()`/`is_file()` if you need to test for file presence or type before writing, although catching exceptions is acceptable.
* Make sure your file path is correct and you have write permission. Overwriting an existing file happens silently with `write_text()`, so treat overwrites with caution.

---

## Common Pitfalls

* **Assuming append behaviour**: `write_text()` always overwrites the file; it does **not** support an append mode. To append, you must open and write manually.
* **Large-file inefficiency**: Because it writes the entire string in one go, if you build a huge string in memory you may impact memory usage; for incremental writes use streaming.
* **Misunderstanding return value**: The return value is the number of characters written, not bytes, which may cause confusion when multibyte encodings are involved.
* **Encoding mis-match**: If you write using a given encoding and later read with a different one (or default system encoding differs), you may see decoding errors or garbled text.
* **Silent overwrite risk**: Because it overwrites by default, accidental data loss can occur if you target an existing file without caution.
* **Disk-full risk**: As noted, if the disk becomes full during write, you might end up with an unexpected result (e.g., zero bytes) rather than partial or full data.

---
