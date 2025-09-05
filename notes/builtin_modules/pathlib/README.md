# `pathlib`

Python’s **`pathlib`** is a standard-library module introduced in version 3.4 that furnishes an **object-oriented interface for filesystem paths**.

## 1. Motivations and Philosophical Rationale

Handling file paths using traditional string manipulation (`os.path`, `glob`, `shutil`) often entails verbosity, platform-specific concerns, and error-prone logic.

`pathlib` was designed to:

* Enable **cross-platform consistency**, abstracting differences between Windows and POSIX path semantics.
* Offer a **cleaner, Pythonic, and more maintainable API** centered on `Path` objects.

---

## 2. Core Class Hierarchy

The module distinguishes between two broad categories:

### Pure Paths

* Abstract, computational-only path types that do **not** interact with the filesystem (no I/O).
* Classes include `PurePath`, with OS-specific variants: `PurePosixPath` and `PureWindowsPath`.
* Useful when **manipulating path strings across platforms** without performing operations on the actual filesystem.

### Concrete Paths

* Derived from pure paths, these allow real filesystem I/O.
* Including `Path`, which instantiates to either `PosixPath` or `WindowsPath`, depending on the operating system.

As the documentation states: *"If you’ve never used this module before... Path is most likely what you need."*.

---

## 3. Key Features and Functionalities

### Object-Oriented Path Manipulation

* Instantiate paths via `Path("some/path.txt")`, `Path.cwd()`, or `Path.home()`.
* Combine paths using the `/` operator; or alternatively, `.joinpath()`.

### Path Properties and Components

* Access filename with `.name`, parent directory via `.parent`, and file extension via `.suffix`; while `.stem` gives the name without extension.

### File System Queries and Operations

* Check existence and type: `.exists()`, `.is_file()`, `.is_dir()`.
* Read/write with built-in methods: `.read_text()`, `.write_text()`, `.read_bytes()`, `.write_bytes()`.
* Create files or directories using `.touch()`, `.mkdir()` (supports `exist_ok=True` to suppress errors).

### Efficient Traversal and Pattern Matching

* Iterate directory contents via `.iterdir()`.
* Pattern-based file search using globbing: `.glob("*.py")` and recursive search `.rglob("**/*.py")`.

### Modification and Manipulation

* Modify filename components using `.with_name()`, `.with_suffix()` (and `.with_stem()` in Python 3.9+).
* Rename or replace files via `.rename()` or `.replace()`.

### Integration with Standard Tools

* `Path` objects integrate seamlessly: you can pass them directly to built-in functions like `open()` or `shutil.copy()`; no need to convert to strings.

---
