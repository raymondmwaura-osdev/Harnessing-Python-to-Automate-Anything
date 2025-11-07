# `pytest` and Python Classes

## Overview of Class-Based Tests in pytest

pytest allows tests to be written as individual functions or grouped into classes. When grouping into classes, the class name typically begins with `Test` (or matches the configured `python_classes` pattern) and test methods begin with `test_`.
Using classes for grouping is largely a matter of organisation rather than requirement; pytest treats classes mostly as containers for grouping related test methods, not as full xUnit style `TestCase` classes with complex setup/teardown.

---

## Instance Creation and `__init__` Behaviour

### Unique Instance Per Test Method

One key behavioural point: for each test method in a test class, pytest creates a **new instance** of that class. That means attributes set in one method instance do *not* carry over to another test method instance.
Example illustration:

```python
class TestExample:
    value = 0

    def test_one(self):
        self.value = 1
        assert self.value == 1

    def test_two(self):
        assert self.value == 1  # fails, because a fresh instance is used
```

In this example, `test_two` does *not* see `value=1` from `test_one` because they use separate class instances.

### `__init__` Method Restriction

pytest by default **does not collect classes** that define a custom `__init__` method (with parameters or any non-default constructor) for test discovery. If a class named `Test…` has a non-trivial `__init__`, pytest will issue a `PytestCollectionWarning` such as:

> “cannot collect test class ‘Test…’ because it has a **init** constructor”

The reason: pytest expects to instantiate test classes using a no-argument constructor; introducing your own `__init__` breaks this assumption and may prevent test collection or cause unpredictable behaviour.
Therefore, avoid defining `__init__` in test classes unless you know exactly how pytest will behave and collection is manually configured.

---

## Organisational and Functional Considerations

### Grouping and Shared Setup

Classes help you group logically related test methods (for example, all tests for a class under test). They also permit class-level fixtures (scope="class") to apply to all methods in the class. This grouping can improve readability and maintainability.
However, because each test method uses a fresh class instance, **sharing instance-state across methods** via class attributes (rather than fixtures) may lead to confusion or unintended test interactions.

### Interaction with Fixtures

You can use fixtures in class-based tests just like in function-based tests: by declaring fixture names as method parameters. Fixtures may be applied at method level, class level, module level, etc. pytest handles injection the same way for methods inside classes.
If you want setup/teardown across all methods in a class, it’s preferable to use a `@pytest.fixture(scope="class")` or `setup_class()` rather than relying on `__init__`.

### Collection and Discovery Behaviour

By default, pytest will collect:

* Files matching `test_*.py` or `*_test.py`.
* Classes whose names begin with `Test` (or match `python_classes` pattern) and contain methods whose names begin with `test_`.
  If a class defines a custom `__init__`, pytest may skip it or warn. Also, test methods must follow naming conventions, else they won’t be discovered.

---

## Best Practices and Pitfalls

**Recommended practices:**

* Use classes primarily for grouping tests, not for managing state via `__init__`.
* Do *not* define a custom `__init__` in test classes unless absolutely necessary; instead use fixtures or `setup_class()` for initialization.
* Rely on fresh instances per method to maintain test isolation; avoid depending on class-instance state across methods.
* Use class-level fixtures if many methods share setup/teardown logic, with appropriate fixture scope (e.g., `scope="class"`).
* When using classes, keep methods independent (each test method should not depend on the result or side-effects of another).
* If you need inheritance of test logic (e.g., subclassing a base test class), ensure the base class has no custom `__init__` and that inherited methods conform to pytest naming and discovery rules.

**Common pitfalls:**

* Defining `__init__` causes test class to be skipped silently or trigger collection warnings.
* Writing test methods that rely on prior test methods because instances are fresh each time leads to brittle tests.
* Adding non-test methods to classes named `Test…` without proper naming can cause confusion in discovery.
* Using class attributes for mutable state across methods increases risk of tests influencing each other (best avoided).

---
