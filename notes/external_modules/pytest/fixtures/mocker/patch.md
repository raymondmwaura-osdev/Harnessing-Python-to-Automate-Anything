# `mocker.patch`

## Overview of `mocker.patch`

`mocker.patch` is a method provided by the **pytest-mock** plugin that allows you to **temporarily replace objects, functions, or attributes** during a test. It is essentially a pytest-friendly wrapper around `unittest.mock.patch`, with the advantage that cleanup is automatic at the end of the test.

The typical uses are:

* Replacing a function or method so it returns a controlled value.
* Intercepting calls to external services (APIs, file systems, databases).
* Tracking how a function or object is called.

---

## Basic Syntax

```python
mocker.patch(target, new=DEFAULT, **kwargs)
```

**Parameters**:

* `target` (str): The fully-qualified name of the object to patch (e.g., `"module.Class.method"`).
* `new` (optional): The object to replace the target with. If omitted, a `Mock` object is created automatically.
* `**kwargs`: Additional arguments, e.g., `return_value`, `side_effect`.

**Return value**: The mock object that replaced the original target.

---

## Important Principle: Patch Where It’s Used

A common mistake is patching the wrong namespace. You must patch the object **where your code under test references it**, not where it was originally defined.

Example:

```python
# my_module.py
import requests

def get_data():
    return requests.get("https://api.example.com").json()
```

To mock `requests.get`:

```python
# test_my_module.py
def test_get_data(mocker):
    mock_response = mocker.Mock()
    mock_response.json.return_value = {"id": 1}
    
    mocker.patch("my_module.requests.get", return_value=mock_response)
    
    from my_module import get_data
    result = get_data()
    
    assert result == {"id": 1}
    mock_response.json.assert_called_once()
```

Notice that we patch `"my_module.requests.get"`, not `"requests.get"`.

---

## Setting Return Values and Side Effects

`mocker.patch` allows you to control **what happens when the mocked object is called**.

### Using `return_value`:

```python
def test_return_value(mocker):
    mocker.patch("my_module.requests.get", return_value={"data": 42})
    from my_module import get_data
    result = get_data()
    assert result == {"data": 42}
```

### Using `side_effect`:

`side_effect` can simulate exceptions, multiple return values, or custom behavior.

```python
def test_side_effect(mocker):
    def fake_get(url):
        return {"url_called": url}
    
    mocker.patch("my_module.requests.get", side_effect=fake_get)
    from my_module import get_data
    result = get_data()
    assert result == {"url_called": "https://api.example.com"}
```

---

## Mocking Attributes and Methods on Objects

You can also patch **object attributes** using `mocker.patch.object`.

```python
class Service:
    def method(self):
        return "real"

def test_patch_object_method(mocker):
    service = Service()
    mocker.patch.object(service, "method", return_value="mocked")
    assert service.method() == "mocked"
```

---

## Asserting Calls

After patching, you can inspect **how the mock was called**:

```python
mock = mocker.patch("my_module.requests.get")
from my_module import get_data
get_data()

# Assertions
mock.assert_called_once()
mock.assert_called_with("https://api.example.com")
```

This is critical for **behavior verification** in unit tests.

---

## Advantages of `mocker.patch`

* **Automatic cleanup**: The original object is restored after each test.
* **Pytest integration**: Works seamlessly with fixtures.
* **Flexible configuration**: Can set `return_value`, `side_effect`, or replace with a custom object.
* **Supports spying**: You can combine with `mocker.spy` to track real calls.

---

## Best Practices

* Always patch **in the module where it is used**, not where it was defined.
* Use `return_value` for simple replacements and `side_effect` for dynamic behavior or exceptions.
* Avoid over-mocking. Only mock what the test truly depends on.
* Assert **behavior**, not internal implementation details.

---
