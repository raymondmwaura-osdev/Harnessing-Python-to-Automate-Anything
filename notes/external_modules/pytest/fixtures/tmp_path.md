# `tmp_path`

## What is `tmp_path`

The `tmp_path` fixture provides each test function with a **fresh temporary directory**, represented as a `pathlib.Path` object, which is unique to that test invocation.
Concrete key points:

* It replaces the older `tmpdir` fixture (which returned `py.path.local` objects) with a `pathlib.Path`-based API, aligning with modern Python path handling.
* The directory provided by `tmp_path` is created under pytest’s base temporary directory. By default the base directory is something like `{temproot}/pytest-of-{user}/pytest-{num}/{testname}/`.
* Because each test function receives its own directory, this supports isolation of filesystem state across tests (reducing side-effects and interference).

---

## What’s important to know

### Scope and lifecycle

The `tmp_path` fixture is function-scoped; meaning each test function invocation receives a new directory.
Because the directory is created per invocation, any files or directories created during the test live only inside that directory. When the pytest session ends (or the directory is no longer referenced), cleanup is managed by pytest (though the default retention behavior keeps a few runs’ directories around for debugging).

### Directory location, retention, and customization

* By default the base temporary directory is under the system’s temporary directory (as determined by `tempfile.gettempdir()`), combined with user/suite-specific parts.
* You can override the base temporary directory by passing `--basetemp=MYDIR` to pytest, which causes pytest to place all `tmp_path` directories under `MYDIR/{testname}/`. Note that when using `--basetemp`, pytest clears the directory before each test run; so only the most recent run’s data remains.
* The default retention of old temporary base directories: pytest by default keeps the last **3** runs’ temporary directories (unless overridden).

### Usage and API

Because `tmp_path` yields a `pathlib.Path`, you can use the familiar pathlib operations: creating sub-directories with `/`, `.mkdir()`, creating files with `.write_text()`, reading them with `.read_text()`, iterating via `.iterdir()`, checking existence via `.exists()`, etc. Example:

```python
def test_create_file(tmp_path):
    subdir = tmp_path / "sub"
    subdir.mkdir()
    file = subdir / "hello.txt"
    file.write_text("content", encoding="utf-8")
    assert file.read_text(encoding="utf-8") == "content"
```

### Differences to older fixtures

* The older `tmpdir` fixture returns `py.path.local` objects rather than `pathlib.Path`. `tmp_path` is preferred now.
* If you need directories with broader scope (e.g., session-wide directory), you should use the `tmp_path_factory` fixture instead. The `tmp_path` fixture is not designed for sharing between tests beyond a single function invocation.

### Potential limitations or caution

* Although each test gets a unique directory, if you run the *same* test function concurrently in multiple processes (for example using `pytest-xdist`) you may encounter clashes or unintended sharing unless the base directory is uniquely configured.
* Relying on the directory path for long-term persistence (across test sessions) is discouraged: the cleanup/retention policy is controlled by pytest and paths may be removed automatically.
* Tests that rely on absolute deterministic paths (for external systems) should not assume the base path beyond what `tmp_path` provides; better to create sub-paths within it.

---

### When to use `tmp_path`

* When a test needs to create files or directories on disk as part of its behavior (e.g., writing some data, reading back, verifying file operations).
* When you want to ensure that the test’s filesystem side-effects are isolated and will not interfere with other tests, or leave residue on disk after the test run.
* When you want portability of your tests across environments/OSes: `tmp_path` abstracts away the system temporary directory details and exposes a `pathlib.Path` API which is cross-platform.
* When you prefer modern Python path handling (`pathlib`) rather than legacy `py.path.local`.

---

### Example

```python
import pytest

def write_data(path: str, data: str):
    with open(path, "w", encoding="utf-8") as f:
        f.write(data)

def read_data(path: str) -> str:
    with open(path, "r", encoding="utf-8") as f:
        return f.read()

def test_write_and_read(tmp_path):
    # Create a sub-directory within the temporary directory
    sub = tmp_path / "mydir"
    sub.mkdir()
    # Define a file path inside it
    file_path = sub / "test.txt"
    # Write data
    test_content = "Hello, pytest!"
    file_path.write_text(test_content, encoding="utf-8")
    # Read data
    result = file_path.read_text(encoding="utf-8")
    assert result == test_content
    # Verify the structure
    assert (tmp_path / "mydir").exists()
```

In this example:

* `tmp_path` gives a fresh directory for the test.
* We create a sub-directory, write a file, read it back, assert contents and existence.
* No manual cleanup is required; once the test finishes, pytest handles the directory (subject to retention policy).

---
