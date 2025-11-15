# `mocker.Mock.side_effect`

## Key Topics

- [Overview](#overview)  
- [Allowed Values](#allowed-values)  
  - [Callable](#callable)  
  - [Iterable](#iterable)  
  - [Exception](#exception)  
- [Examples](#examples)  
- [Interaction With Other Parameters](#interaction-with-other-parameters)  
- [Best Practices](#best-practices)  
- [Common Pitfalls](#common-pitfalls)  

---

## Overview

+ The `side_effect` attribute of a mock (e.g., `mocker.Mock`) allows you to define custom behavior when the mock is called. Rather than simply returning a fixed `return_value`, you can make the mock *raise exceptions*, *return different values on each call*, or *invoke a custom function*.  
+ This exists to simulate more realistic or complex behaviors in tests; for instance, simulating intermittent failures, raising specific exceptions, or generating values dynamically.  
+ In general, when the mock is invoked:
  - If `side_effect` is a **callable**, that function is called with the same arguments as the mock; its return value becomes the mock call’s return value (unless it returns a special sentinel).  
  - If `side_effect` is an **iterable**, each call to the mock will yield the next element in the iterable. If that element is an exception, the mock raises it. If it's a normal value, the mock returns it.  
  - If `side_effect` is explicitly set to an **exception instance or class**, then every call to the mock will raise that exception.  

---

## Allowed Values

### Callable  

+ **Meaning**: A function (or any callable) that will be invoked every time the mock is called.  
+ **Behavior**: The callable receives the same positional and keyword arguments as the mock; whatever it returns becomes the result of the mock call (unless it returns `unittest.mock.DEFAULT`, in which case the mock uses its `return_value`).
+ **Special Cases**:  
  - If the callable raises an exception, the mock call propagates that exception.  
  - If the callable returns `unittest.mock.DEFAULT`, the mock reverts to using its `return_value`.

#### Example

```python
def my_side_effect(x, y=0):
    if x + y > 10:
        raise ValueError("Sum too large")
    return x + y

m = mocker.Mock()
m.side_effect = my_side_effect

assert m(3, y=4) == 7
with pytest.raises(ValueError):
    m(10, y=5)
```

#### Explanation

* On the first call, `my_side_effect` returns `7`, so the mock returns `7`.
* On the second call, `my_side_effect` raises `ValueError`, and so does the mock.

---

### Iterable

* **Meaning**: A list, tuple, or any iterable that provides values (or exceptions) to be used on successive calls.
* **Behavior**: The mock will consume the iterable: on the first call it uses the first item, on the second call the second item, etc.
* **Special Cases**:

  * If an item in the iterable is an exception instance (e.g., `ValueError("fail")`), then that exception is raised when the mock is called at that point.
  * If the iterable is shorter than the number of calls, a `StopIteration` will be raised when the iterable is exhausted.

#### Example

```python
m = mocker.Mock()
m.side_effect = [1, 2, ValueError("oops"), 4]

assert m() == 1
assert m() == 2
with pytest.raises(ValueError):
    m()
assert m() == 4
```

#### Explanation

* First call → returns `1`
* Second call → returns `2`
* Third call → raises `ValueError("oops")`
* Fourth call → returns `4`

---

### Exception

* **Meaning**: A direct exception class or instance assigned to `side_effect`.
* **Behavior**: Every time the mock is called, it raises that exception.
* **Special Cases**: If you assign an exception *type*, then each call raises a **new instance** of that exception; if you assign an *instance*, the same object is reused (though semantics are typically the same, since exceptions are only “raised”).

#### Example

```python
m = mocker.Mock()
m.side_effect = RuntimeError("fatal")

with pytest.raises(RuntimeError):
    m()

# On every call, the same exception type (and message) is raised
with pytest.raises(RuntimeError):
    m()
```

---

## Examples

Here are a few succinct but practical patterns for using `side_effect` with `mocker.Mock` in pytest-mock:

1. **Simulating multiple return values or failures:**

   ```python
   def test_multiple_calls(mocker):
       service = mocker.Mock()
       service.call.side_effect = ["ok", "still ok", RuntimeError("failed")]

       assert service.call() == "ok"
       assert service.call() == "still ok"
       with pytest.raises(RuntimeError):
           service.call()
   ```

2. **Injecting dynamic behavior:**

   ```python
   def test_dynamic(mocker):
       def effect(a, b):
           return a * b

       calculator = mocker.Mock()
       calculator.multiply.side_effect = effect

       assert calculator.multiply(3, 4) == 12
       assert calculator.multiply(5, 6) == 30
   ```

3. **Forcing an exit (like `sys.exit`):**

   ```python
   def test_exit(mocker):
       exit_mock = mocker.Mock()
       exit_mock.side_effect = SystemExit

       with pytest.raises(SystemExit):
           some_function_that_calls_exit(exit_mock)

       exit_mock.assert_called_once()
   ```

---

## Interaction With Other Parameters

Here is how `side_effect` interacts with other `Mock` parameters:

* **`return_value`**:
  When `side_effect` is a callable and returns `unittest.mock.DEFAULT`, the mock falls back to its `return_value`.
  If `side_effect` is not callable (e.g., an iterable or exception), `return_value` is ignored for those calls.

* **`autospec`**:
  If you use `mocker.patch(..., autospec=True)`, you’re still free to set `side_effect` on the mock. The mock will enforce the spec of the original function/class while applying the side effect behavior.

* **`name`**:
  You can set a name for the mock (for debugging) independently of `side_effect`. The name does not influence how `side_effect` works.

* **With `mocker.spy`**:
  Note: in `pytest-mock` version ≥ 2.0, the spy object tracks calls but does *not* use `side_effect` in the same way: `mocker.spy` uses different attributes (`spy_return`, `spy_exception`), not `return_value` or `side_effect`.

---

## Best Practices

* Use **iterable side effects** when you're testing sequential behavior (e.g., first call succeeds, second fails).
* Use **callable side effects** when behavior depends on the input arguments or when more complex logic is needed.
* Be explicit in tests: when using side effects, clearly document in test code what behavior is being simulated (e.g., “first call fails, second call succeeds”).
* Reset or recreate mocks between tests, especially when using iterable side effects, to avoid cross-test contamination.
* Avoid over-mocking: only mock what’s necessary for the specific unit test; leave other behavior as real code, so tests remain meaningful.

---

## Common Pitfalls

* **Running out of iterable**: If your `side_effect` is an iterable and you call the mock more times than there are items, you’ll hit a `StopIteration` error.
* **Confusing `return_value` and `side_effect`**: Remember, `return_value` is only used when `side_effect` is either not set or returns `DEFAULT`.
* **Using wrong patch target**: A very common mistake in pytest-mock is patching in the wrong namespace. If you patch a function where it is *defined* rather than where it is *used*, your side_effect may not be triggered.
* **Stateful side-effects across tests**: If your side_effect callable or iterable has internal mutable state (counters, lists), and you reuse the mock across tests, tests may interfere with each other.
* **Swallowing exceptions inadvertently**: If your side_effect function catches exceptions internally (or returns a value even when you intended to raise), then the mock won't propagate the exception, which may hide bugs.

---
