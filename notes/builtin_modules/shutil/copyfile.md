# `shutil.copyfile`

## Key Topics

- [Overview](#overview)
- [Syntax, Parameters, and Return Value](#syntax-parameters-and-return-value)
- [Errors and Exceptions](#errors-and-exceptions)
- [Examples](#examples)
- [Practical Use Cases](#practical-use-cases)
- [Best Practices](#best-practices)
- [Common Pitfalls](#common-pitfalls)
- [Notes and References](#notes-and-references)

---

## Overview

The `shutil.copyfile` function provides a direct and efficient mechanism for copying the **contents** of one file to another, excluding any form of file metadata. Conceived as a minimalistic file operation within Python's high-level `shutil` library, it ensures a precise byte-for-byte transfer. This function is particularly useful when preservation of file attributes (e.g., permissions or timestamps) is unnecessary.

---

## Syntax, Parameters, and Return Value

```python
shutil.copyfile(src, dst, *, follow_symlinks=True)
```

* The function signature requires both the source (`src`) and destination (`dst`) arguments in positional form.
* `src`: `str`; Path to the existing source file.
* `dst`: `str`; Path to the destination file; must include the full target filename and not merely a directory. If the destination already exists, it will be overwritten.
* `follow_symlinks`: `bool` (keyword-only, default `True`); Determines whether, for symlinks, the function should copy the file to which the symlink points (`True`) or replicate the symlink itself (`False`).

**Return value**:

* Returns a `str` representing the path of the newly created destination file.

---

## Errors and Exceptions

* **`SameFileError`**: Raised if `src` and `dst` refer to the same path.
* **`IsADirectoryError`**: Raised if the destination `dst` is a directory rather than a complete filename.
* **`PermissionError`**: Raised when the process lacks write permission at the destination.
* **`IOError`** or **`OSError`**: May arise if the destination directory does not exist or for other general filesystem errors.
* **Special Files**: Attempting to copy special files (e.g., block devices, pipes) may result in errors.

---

## Examples

1. **Minimal usage: simple file copy**

   ```python
   import shutil
   shutil.copyfile('source.txt', 'destination.txt')
   ```

   Copies the contents of `source.txt` into `destination.txt`, replacing it if it exists.

2. **Handling common exceptions**

   ```python
   import shutil

   try:
       shutil.copyfile('a.txt', 'b.txt')
       print("Copied successfully.")
   except shutil.SameFileError:
       print("Source and destination are the same file.")
   except IsADirectoryError:
       print("Destination is a directory.")
   except PermissionError:
       print("Permission denied.")
   except Exception as e:
       print(f"Unexpected error: {e}")
   ```

   Demonstrates robust handling of key error conditions.

3. **Controlling symlink behavior**

   ```python
   shutil.copyfile('link_to_file', 'copy.txt', follow_symlinks=False)
   ```

   If `link_to_file` is a symlink, this command replicates the symlink instead of copying the target's contents.

---

## Practical Use Cases

* **Renaming files without preserving metadata**; useful for log rotation or file versioning.
* **Extracting and duplicating raw content** for processing or analysis where metadata is irrelevant.
* **Simplifying file copying inside automated scripts or pipelines** when metadata preservation is unnecessary.

---

## Best Practices

* **File existence check**: Confirm both source existence and destination directory validity before invoking the function.
* **Exception safety**: Wrap calls in `try…except` to manage `SameFileError`, permission issues, and I/O errors.
* **Environment-aware behavior**: Use `follow_symlinks=False` when working across symbolic links and you intend to preserve structure rather than content.
* **Performance considerations**: Starting in Python 3.8, `shutil.copyfile` may utilize platform-specific fast-copy syscalls (e.g., `sendfile` on Linux, `fcopyfile` on macOS), greatly enhancing performance on large files.

---

## Common Pitfalls

* **Metadata loss**: Only file content is copied; permissions, timestamps, ownership, and extended attributes are not preserved.
* **Incorrect destination specification**: Passing a directory path (rather than a filename) triggers an `IsADirectoryError`.
* **Overwriting critical files**: Without explicit checks, the function replaces existing destination files, possibly leading to data loss.
* **Symbolic link confusion**: The default behavior follows symlinks; unexpected when you intend to replicate link structure.

---

## Notes and References

* **Platform-specific metadata limitations**: Even higher-level functions like `shutil.copy` or `copy2` cannot preserve all metadata reliably; POSIX platforms may drop ownership and ACLs; macOS may lose resource forks; Windows may not retain alternate data streams.
* **Fast-copy enhancements** (Python 3.8+): The internal implementation may use efficient kernel-based copy operations, offering significant performance gains.

---
