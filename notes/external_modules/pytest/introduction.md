# Introduction to pytest

## What is pytest

The framework known as pytest is an open-source testing tool for the Python programming language. It is designed to enable developers to write test code in a concise and readable manner, yet it is fully capable of scaling to complex testing scenarios (such as functional, integration or end-to-end tests) when needed.

Some of pytest’s distinguishing attributes include:

* **Automatic test discovery**: pytest automatically finds test modules and functions following naming conventions (for example filenames beginning with `test_` or ending with `_test.py`).
* **Expressive syntax**: Developers write simple Python functions and `assert` statements rather than needing verbose boilerplate code.
* **Extensibility**: A strong ecosystem of plugins and extensions supports advanced features, such as fixtures for setup/teardown, parameterised testing, and rich reports.

---

## Why pytest matters

Selecting the right testing framework is a pragmatic choice in software development, and pytest matters because it brings several practical benefits to the table:

* **Reduces friction for writing tests**: Because pytest allows straightforward test definitions and fewer constraints, more tests tend to get written, increasing code-coverage and quality.
* **Improves readability and maintainability**: Test code written with pytest tends to be shorter and easier to understand, which makes maintaining tests alongside evolving code simpler.
* **Supports scalability**: As projects grow in size or complexity, pytest’s features (fixtures, parametrisation, plugins) help maintain a manageable test suite rather than letting testing become a bottleneck.
* **Integrates with workflow and tools**: pytest works with various development workflows, continuous integration systems, and can integrate with other testing styles or legacy tests.

---

## How to install pytest

Before writing tests, you must install pytest in your Python environment. The following subsections describe the typical installation process and some recommended practices.

### Pre-requisites

* Ensure you have a compatible version of Python. For recent pytest releases, this means Python 3.7 or newer (or PyPy3).
* It is strongly recommended to use a virtual environment (such as `venv`, `virtualenv`, or Conda) to isolate your testing dependencies from global Python packages; this avoids version conflicts and enhances reproducibility.

### Installation via pip

Once a virtual environment is activated, install pytest with the following command:

```bash
pip install -U pytest
```

The `-U` flag ensures you install or upgrade to the most recent version available.

### Verifying the installation

After installation completes, verify pytest is available and determine its version by running:

```bash
pytest --version
```

The command will report the pytest version and the path from which it is imported.

### Installation via alternative managers

If you prefer Conda or another package manager, pytest is available on the Python Package Index (PyPI) and commonly via conda-forge. For example:

```bash
conda install -c conda-forge pytest
```

### Additional environment considerations

* On some systems, especially Windows, you may need to ensure the `Scripts` directory of the virtual environment is in your `PATH`, so that the `pytest` command is recognised.
* If you have multiple Python versions installed, ensure you are installing pytest into the correct version (e.g., `python3 -m pip install pytest`).

---

Here is a continuation of the tutorial on **pytest**: covering its syntax and giving a **simple example**.

---

## Test Syntax Fundamentals

Before diving into more advanced features, it is important to understand the basic syntax rules and conventions that pytest employs.

### File and Function Naming Conventions

* Test files should be named so that pytest will discover them by default. For example, filenames beginning with `test_` or ending with `_test.py`.
* Within those files, test functions should typically begin with `test_`. For example: `def test_addition(): …`
* Optionally, tests can be grouped in classes whose names begin with `Test`. The class should not require a base class.

### Writing Assertions

* Unlike some older test frameworks, pytest allows you to use plain `assert` statements for checking expected behaviour.
* One of pytest’s features is *assertion rewriting*, meaning when an `assert` fails, pytest will often provide rich information about what the expression evaluated to, making diagnosing failure easier.
* The typical pattern inside a test function is:

  1. Arrange (set up input or context)
  2. Act (call code under test)
  3. Assert (check the result); often called the “AAA” pattern.

### Running Tests

* From the command line (in project root), simply running `pytest` will discover and execute tests in suitable files/directories.
* Optionally, a specific file or even a specific test function can be targeted.
* The output will show which tests passed or failed, and for failures will show the assertion failure details.

---

## A Simple Example

### Code Under Test

Suppose you have a Python module `calculator.py` with a simple function:

```python
# calculator.py
def add(a, b):
    return a + b
```

### Test Module

Create a test file named `test_calculator.py` in the same directory (or in a `tests/` directory). In that file:

```python
# test_calculator.py
from calculator import add

def test_add_positive_numbers():
    result = add(2, 3)
    assert result == 5

def test_add_negative_and_positive():
    result = add(-1, 4)
    assert result == 3
```

### Running the Test

From the command line, run:

```bash
pytest
```

You should see pytest discover both test functions (in `test_calculator.py`), execute them, and report something like:

```
============================= test session starts =============================
collected 2 items

test_calculator.py ..                                                [100%]

============================== 2 passed in 0.03s ==============================
```

If, however, one assertion were incorrect (for example `assert result == 4`), pytest would show a failure, and it would display the actual value of `result` and show you what expression failed.

### Interpretation

* Each test function is independent; pytest treats each `test_…` function as a separate test case.
* Using plain `assert` means you do not need to import a special assertion library; pytest handles introspection.
* The test output clearly shows which tests passed/failed and provides useful diagnostic information in case of failure.
* The naming conventions (file name starts with `test_` and function names start with `test_`) allow pytest to pick them up automatically.

---
