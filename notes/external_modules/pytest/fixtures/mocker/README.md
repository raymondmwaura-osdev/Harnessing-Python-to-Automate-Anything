# `mocker`

## Overview

The `mocker` fixture is a convenience wrapper around Python’s standard `unittest.mock` facility that integrates seamlessly with pytest. Its principal objectives are:

* to simplify patching of functions, methods, classes, or attributes within pytest test functions;
* to automatically handle teardown of mock modifications at the end of each test, avoiding residual side‑effects;
* to present a cleaner, pytest‑idiomatic API (versus directly using `unittest.mock.patch`) for typical mocking needs.

---

## Installation and Setup

Before using the `mocker` fixture, install pytest‑mock in your environment:

```bash
pip install pytest‑mock
```

The plugin registers the `mocker` fixture automatically with pytest.

In your `pytest.ini` or other configuration you may leave default settings; no further special configuration is required for basic usage.

---

## Basic Usage

### Patching a function

Consider the following production code snippet in `file module.py`:

```python
import os

def remove_file(path):
    os.remove(path)
```

A test using the `mocker` fixture could be written as:

```python
# test_module.py
from module import remove_file

def test_remove_file(mocker):
    mocker.patch('module.os.remove')
    remove_file('/tmp/foo')
    module.os.remove.assert_called_once_with('/tmp/foo')
```

Here:

* `mocker.patch('module.os.remove')` replaces `os.remove` with a mock for the duration of the test.
* After invoking `remove_file`, we assert that the patched function was invoked with the expected argument.
* At test exit, the patch is undone automatically.

### Setting return values or side effects

Suppose a function that calls an external service:

```python
# api_client.py
import requests

def fetch_data(url):
    r = requests.get(url)
    return r.json()
```

A test could use `mocker` as follows:

```python
# test_api_client.py
from api_client import fetch_data

def test_fetch_data(mocker):
    mock_response = mocker.Mock()
    mock_response.json.return_value = {'key': 'value'}
    request_get_mock = mocker.patch('api_client.requests.get', return_value=mock_response)

    result = fetch_data('http://example.com')
    assert result == {'key': 'value'}
    request_get_mock.assert_called_once_with('http://example.com')
```

Key points:

* We created a `mock_response` object via `mocker.Mock()`.
* We set `mock_response.json.return_value` to the desired value.
* We patched `requests.get` to return our `mock_response` object.
* Then we executed the production function and asserted both behavior (return value) and call‐interaction.

---

## Common Methods Provided by `mocker`

The `mocker` fixture supports several methods (beyond simple `patch`) to facilitate diverse mocking scenarios:

* `mocker.patch(target, **kwargs)`: Replace the target (function, method, attribute) with a mock.
* `mocker.patch.object(obj, attribute, **kwargs)`: Patch an attribute on an object or class.
* `mocker.patch.dict(dictionary, values, **kwargs)`: Temporarily modify a dictionary (e.g., environment variables) for the test.
* `mocker.spy(obj, attribute)`: Create a spy object; i.e., real function calls are executed, but calls and arguments can still be inspected.
* `mocker.stopall()`: Manually undo all patches (rarely needed since teardown is automatic).

These methods allow for flexible control over dependencies and behavior during test execution.

---

## Best Practices and Important Caveats

* **Patch where it is used**: Always patch the *reference* seen by the module under test, not necessarily the original definition. Incorrect patching is a common source of test fragility.
* **Scope of patches**: Since the `mocker` fixture is function‐scoped by default, each test gets a fresh environment. If you need broader scope (e.g., module or session), you may still need to structure fixtures accordingly.
* **Avoid over‐mocking**: Only mock external dependencies or behaviour that you cannot (or should not) execute for real in a unit test context. Excessive mocking can reduce test value and maintainability.
* **Assert behaviour, not internals**: Use mocks to assert *what* happens (calls, returns) rather than testing inaccessible internal state.
* **Teardown is automatic**: One of the advantages of `mocker` is that patching is undone automatically at the end of a test, avoiding contamination of subsequent tests.
* **Combine with fixtures**: You can create custom fixtures that depend on `mocker` to set up common mocks for many tests, enhancing reuse and readability.

---
