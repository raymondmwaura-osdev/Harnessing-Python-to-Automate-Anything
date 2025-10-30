# Project Structure and Test Organisation

## Recommended Directory Layouts

Here are two common layouts:

* **Flat tests folder**

  ```
  my_project/
    src/
      module_a.py
      module_b.py
    tests/
      test_module_a.py
      test_module_b.py
  ```

  Here, all test files are grouped under a single `tests/` directory.
* **Mirrored structure**

  ```
  my_project/
    src/
      module_a/
        __init__.py
        component_x.py
      module_b/
        __init__.py
        component_y.py
    tests/
      module_a/
        test_component_x.py
      module_b/
        test_component_y.py
  ```

  In this layout the test directories mirror the source-code structure, making it easy to locate tests for a given module.

---

## Naming Conventions

* **Test files** should by default be named starting with `test_` or ending with `_test.py`. For example: `test_utils.py`, `calculator_test.py`.
* **Test functions** inside those files should similarly start with `test_`, e.g., `def test_addition(): …`.
* **Test classes** (if used) should start with `Test` (no `__init__`) and contain methods starting with `test_`.
* Keep source code and test code clearly separated (e.g., `src/` vs `tests/`) rather than mixing them, to avoid confusion and import-issues.
* Use descriptive test file and function names (e.g., `test_login_invalid_password` rather than `test_case1`) for clarity.

---

## Test Discovery and Customisation

By default, pytest will scan for files matching its naming conventions (e.g., `test_*.py`) and will recurse into sub-directories unless configured otherwise.  
You can customise these patterns in a configuration file (e.g., `pytest.ini`, `pyproject.toml`) using options like `python_files`, `python_functions`, `python_classes`. Example:

```ini
[pytest]
python_files = check_*.py
python_classes = Check
python_functions = *_check
```

---

## Using `conftest.py` for Shared Fixtures

When many tests require shared setup or fixtures (e.g., initializing a database connection, temporary directories, creating common test objects), a central approach helps.

### What is `conftest.py`?

A file named `conftest.py` placed in a test directory (or sub-directory) is automatically recognized by pytest and used for configuration of fixtures, hooks, and test-suite-wide setup. Fixtures defined in `conftest.py` are available to all test modules in that directory and any sub-directories.

### Scope and Hierarchy

* If you place a `conftest.py` in the root of your `tests/` directory, fixtures defined there are available to all tests.
* You can also have nested `conftest.py` files in sub-directories (for example `tests/api/conftest.py`) which define fixtures specific to that subset of tests. The closer fixture definition takes precedence.
* Tests search *upwards* for fixtures: a test in `tests/subdir/` can use fixtures in `tests/conftest.py` and `tests/subdir/conftest.py`, but not fixtures defined in siblings outside its path.

### Example Usage

Suppose you have:

```
my_project/
  src/
    utils.py
  tests/
    conftest.py
    test_utils.py
```

**`tests/conftest.py`**:

```python
import pytest

@pytest.fixture
def sample_data():
    return { "a": 1, "b": 2 }
```

**`tests/test_utils.py`**:

```python
from src.utils import some_function

def test_some_function(sample_data):
    result = some_function(sample_data)
    assert result == expected_value
```

Here, `sample_data` fixture is available to `test_utils.py` without explicit import.

### Best Practices for `conftest.py`

* Use `conftest.py` for fixtures that are broadly applicable across tests (e.g., session-level fixtures, common test objects).
* Do **not** import your `conftest.py` from test modules; pytest handles injection automatically.
* Avoid putting too much unrelated setup logic into a single `conftest.py` (which can become a “god file”). If many fixtures exist, consider modularising them into separate modules and importing via `conftest.py`.
* Keep fixture scopes appropriate: use function, module, or session scope depending on cost of setup/teardown. Shared fixtures in `conftest.py` often benefit from broader scope.

---

## Putting It All Together

Here’s a suggested workflow for structuring tests and fixtures in a medium-sized Python automation project:

1. Create a dedicated `tests/` directory at the project root.
2. Inside `tests/`, mirror the `src/` module structure where appropriate (e.g., `tests/utils`, `tests/api`).
3. Name test files `test_*.py`. Within each file, name test functions `test_<behaviour>`.
4. At the root of `tests/`, include `conftest.py` that defines common fixtures (e.g., configuration objects, data providers).
5. If a sub-directory requires specific fixtures, add `tests/subdir/conftest.py` with those.
6. Run `pytest` from the project root; ensure discovery picks up all test files. Review results and maintain clarity.
7. Over time, refactor both tests and fixtures: remove duplication, split large test files into logical units, keep `conftest.py` lean and purposeful.

---
