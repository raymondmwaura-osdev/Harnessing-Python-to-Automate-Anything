# Pytest

## Install Mocks

Remember to install `pystest-mock` to be able to use the `mocker` fixture.

```sh
pip install pytest pytest-mock
```

---

## `pathlib.Path` mocks

You can't make mock functions for `pathlib.Path` methods.

```python
# module.py
import pathlib
file = pathlib.Path.cwd() / "file.txt"
file.write_text("Sample text.")

# test_module.py
def test_function(mocker):
    mocker.patch("module.file.write_text") # This will fail.
```

This will fail and raise an error similar to this:

```
FAILED test_module.py::test_function - AttributeError: 'PosixPath' object attribute 'write_text' is read-only
```

This happens mainly because `pathlib.Path` methods are implemented at the C level for performance, and Python's C API makes these attributes immutable descriptors. When you try to assign a mock to a method like `write_bytes`, Python raises an AttributeError because these methods are defined as read-only at the C level.

**Workaround**: Mock the Path constructor.

```python
def test_function(mocker):
    file_mock = mocker.patch("module.file")
    file_mock.write_text.return_value = None
    file_mock.write_text.assert_called_once()
```

---
