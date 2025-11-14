# Mocks

## Motivation and Purpose of Mocking

In unit testing, one often encounters code that depends on external systems (e.g., network APIs, databases, file systems) or on complex internal components that are difficult to isolate reliably. Mocking offers a systematic technique to replace such dependencies with controlled stand‑ins (mock objects), thereby enabling tests to focus on the *unit under test* rather than on its environment or side‑effects.

Benefits of mocking include:

* Shortened feedback loops (tests run faster and are more reliable)
* Predictability and reproducibility (dependencies are simulated, not real)
* Improved ability to test error/edge conditions (e.g., simulate an API error)

---

## Key Tools and Setup

### Mocking Support

There are two principal approaches for mocking:

* Use Python’s built‑in `unittest.mock` module (which provides `Mock`, `patch`, etc.).
* Use the plugin pytest‑mock (which integrates with pytest via a `mocker` fixture) to simplify patching in pytest style.

To install pytest‑mock:

```bash
pip install pytest‑mock
```

---

## Basic Mocking Workflow

### Example: External API call

Consider a simple function in `main.py`:

```python
import requests

def get_data(url: str):
    response = requests.get(url)
    return response.json()
```

In a unit test you do *not* want to make a real HTTP request to `url`. You can instead patch `requests.get` to return a controlled mock response. For example:

```python
# test_main.py
import pytest
from main import get_data

def test_get_data(mocker):
    fake_url = "https://example.com/api"
    mock_response = mocker.Mock()
    mock_response.json.return_value = {"key": "value"}

    mock_requests_get = mocker.patch("main.requests.get",
                                     return_value=mock_response)

    result = get_data(fake_url)

    assert result == {"key": "value"}
    mock_requests_get.assert_called_once_with(fake_url)
```

Here:

* `mocker.Mock()` creates a dummy object.
* `.json.return_value` sets what `response.json()` returns.
* `mocker.patch("main.requests.get", …)` replaces `requests.get` *in the namespace where it is used* (important detail).
* We assert both the return value and that the mock was called with the correct argument.

### Principle: “Mock where used, not where defined”

A common pitfall is patching the wrong namespace. You must patch the dependency *in the module where your code under test looks it up*, not necessarily where the dependency is originally defined.

### Use of `mocker` fixture

The pytest‑mock plugin provides a `mocker` fixture in test functions. It offers methods such as:

* `mocker.patch()`
* `mocker.patch.object()`
* `mocker.mock_open()`
* and others.
  The fixture automatically ensures cleanup of patches at the end of the test.

---

## Types of Mocking Scenarios

### Mocking Constants or Variables

You might need to override a constant to test behaviour under different conditions. Example:

```python
# area.py
PI = 3.14159
def area_of_circle(radius: float) -> float:
    return PI * radius * radius
```

Test:

```python
def test_area_with_mocked_constant(mocker):
    mocker.patch("area.PI", 3.0)
    assert area_of_circle(5) == 75.0
```

### Mocking File System or Time‑based Functions

Functions that write to disk or sleep for some duration can slow tests or cause side‑effects. Use mocks to avoid actual operations.

### Mocking External API or Service Calls

As shown above, you replace HTTP calls (or other remote calls) with predictable responses so your tests remain fast, isolated and reliable.

---

## Best Practices and Caveats

* **Keep unit tests isolated**: Do *not* rely on external systems; mocks help guarantee this.
* **Mock only what you must**: Over‑mocking can lead to tests that no longer meaningfully verify real behaviour. For example, if you mock the entire function under test, you may be testing nothing.
* **Assert behaviour, not implementation**: Focus on external effects (calls, returns) rather than internal details which may change.
* **Avoid mocking too broadly for integration tests**: For higher‑level tests you may prefer to use real dependencies or stubbeds, to validate integration rather than pure isolation.
* **Be cautious with patching names**: Patching the wrong namespace causes mocks to silently fail to intercept the call, yielding flaky tests.
* **Clean up mocks**: When using raw `unittest.mock.patch`, you must ensure proper teardown. Using pytest‑mock’s `mocker` fixture simplifies this.
* **Document what is being mocked**: For maintainability, make clear why each mock exists and what dependency it replaces.

---
