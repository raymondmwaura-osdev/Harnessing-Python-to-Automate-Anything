# Mock Objects

## Introduction

In software testing, the reliability and maintainability of unit tests often depend on the ability to isolate the code under test from its dependencies. **Mock objects** serve as a powerful mechanism for this purpose. They are **test doubles** that simulate the behavior of real objects, enabling developers to control external dependencies, observe interactions, and test edge cases without invoking real implementations.

Mock objects are commonly used with Python’s `unittest.mock` module or the `pytest-mock` plugin in the pytest framework.

---

## Definition and Purpose

A **mock object** is an object designed to imitate the interface and behavior of a real object while providing additional capabilities for testing, including:

1. **Isolation of units of code**: Mocks prevent tests from being affected by external systems such as databases, network APIs, or file systems.
2. **Controlled outputs**: Developers can specify the return values or side effects of mocked functions or methods.
3. **Observation of behavior**: Mocks record how they are called, enabling verification of interactions between the unit under test and its dependencies.

---

## Creation of Mock Objects

### Using `unittest.mock`

```python
from unittest.mock import Mock

mock_obj = Mock()
```

This creates a generic mock object that can simulate almost any callable, method, or attribute.

### Using `pytest-mock`

```python
def test_example(mocker):
    mock_obj = mocker.Mock()
```

Here, the `mocker` fixture from `pytest-mock` provides a pytest-friendly interface for creating and managing mocks.

---

## Core Attributes and Methods

Mock objects are highly flexible and possess several attributes and methods that facilitate testing.

### Call Behavior

* `mock_obj()` invokes the mock. By default, this returns another mock object.
* All calls to a mock are recorded, including positional and keyword arguments.

### Setting Return Values

* `mock_obj.return_value` specifies the value returned when the mock is called.

```python
mock_obj.return_value = 42
assert mock_obj() == 42
```

* `mock_obj.side_effect` allows for more complex behavior, including raising exceptions, returning multiple values sequentially, or executing a custom function.

```python
mock_obj.side_effect = lambda x: x * 2
assert mock_obj(5) == 10
```

### Inspecting Calls

Mock objects maintain comprehensive information about their usage:

* `mock_obj.called` → `True` if called at least once.
* `mock_obj.call_count` → Number of times the mock was invoked.
* `mock_obj.call_args` → Arguments used in the most recent call.
* `mock_obj.call_args_list` → List of all calls with arguments.

Assertions for verifying calls:

* `mock_obj.assert_called()` → Ensures the mock was called at least once.
* `mock_obj.assert_called_once()` → Ensures the mock was called exactly once.
* `mock_obj.assert_called_with(*args, **kwargs)` → Checks the last call’s arguments.
* `mock_obj.assert_called_once_with(*args, **kwargs)` → Checks that exactly one call occurred with specified arguments.

Example:

```python
mock_obj(1, 2)
mock_obj.assert_called_once_with(1, 2)
assert mock_obj.call_count == 1
```

### Nested and Attribute Mocks

Mock objects automatically generate **sub-mocks** for attributes, enabling the simulation of complex hierarchies:

```python
mock_obj.foo.bar.return_value = 10
assert mock_obj.foo.bar() == 10
```

### Resetting a Mock

* `mock_obj.reset_mock()` clears the call history and resets call-related attributes, such as `call_count` and `call_args`.

---

## Specialized Mock Types

* **`MagicMock`**: Extends `Mock` with pre-defined magic methods (e.g., `__len__`, `__getitem__`, arithmetic operators).
* **`AsyncMock`**: Designed to mock asynchronous functions and coroutines.
* **`PropertyMock`**: Used to mock properties (decorated with `@property`).

---
