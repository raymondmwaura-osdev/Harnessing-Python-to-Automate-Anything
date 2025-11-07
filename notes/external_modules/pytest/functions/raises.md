# `pytest.raises`

## Purpose

The function `pytest.raises` serves to **verify that a piece of code raises an expected exception type** during the execution of a test. It addresses the problem of checking error-handling logic within the code under test; namely, ensuring that under certain inputs or conditions the code fails in the correct way (by raising the correct exception) rather than silently proceeding or raising a wrong exception. It is used during the *execution* phase of testing (not setup or teardown) and influences test assertions and reporting: when used properly it becomes part of the test’s verification logic, and when misused it can hide bugs.

---

## Syntax / Signature

Here is the canonical usage for `pytest.raises`:

```python
import pytest

with pytest.raises(ExpectedExceptionType, *, match: str | re.Pattern[str] = None, check: Callable[[BaseException], bool] = None):
    # code block expected to raise ExpectedExceptionType
    ...
```

There is also a **legacy form** (less recommended) that executes a function with arguments and asserts the exception:

```python
pytest.raises(ExpectedExceptionType, func, *args, **kwargs)
```

---

## Parameters

* **`expected_exception`** (type or tuple of types)
  *Type:* `type[E] | tuple[type[E], ...] | None`
  *Default:* `None` (but normally you supply a type)
  *Description:* The exception class (or tuple of exception classes) that the code under test is expected to raise. Subclasses of the provided types are accepted. If `None`, then type matching is not enforced; but at least one of the other parameters (`match` or `check`) must be used.
  *Constraints:* It must derive from `BaseException`; if omitted entirely then you rely solely on `match` or `check`.

* **`match`** (string or regular expression)
  *Type:* `str | re.Pattern[str] | None`
  *Default:* `None`
  *Description:* A regex (or literal string) that must match the string representation of the caught exception (including its `__notes__` if any). This allows verifying not just the type, but the content of the exception message.
  *Constraints:* Only valid when used with the context-manager form; passing `match` alone with a function form may require retrieving `ExceptionInfo.match(...)`. Also, be cautious about special regex characters needing escape.

* **`check`** (callable)
  *Type:* `Callable[[BaseException], bool] | None`
  *Default:* `None`
  *Description:* Introduced in pytest v8.4, this parameter allows defining a custom predicate that receives the caught exception instance and returns `True` if it meets additional user-defined criteria, or `False` otherwise.
  *Constraints:* When provided, it must return `True` for the test to consider the exception match successful. It works in combination with (or in lieu of) `expected_exception` and `match`.

---

## Return Value

When used as a **context manager**, `pytest.raises` returns an `ExceptionInfo` object (often captured as `excinfo`). This object provides access to attributes like `.type`, `.value`, and `.traceback` for further inspection.
When used in the legacy non-context form (`pytest.raises(ExceptionType, func, args…)`), it returns an `ExceptionInfo` object directly.
If the expected exception is *not* raised, the call causes the test to fail (via pytest’s internal `fail()` mechanism). If a different exception is raised, or no exception is raised, the test fails.

---

## Behavioral Description

The behavior of `pytest.raises` unfolds during test execution, not at the test-collection phase. When entering the `with pytest.raises(...)` block, pytest begins monitoring for exceptions. If the expected exception (or one of the expected types) is raised during the block, control passes to the next statement after the block; if not, pytest registers a failure. If the `match` parameter was provided, pytest will check the textual representation of the exception (including its `__notes__`) against the regex or string. If `check` is provided, pytest will pass the caught exception to that callable and treat a `False` result as a failure.

Internally, pytest uses its `ExceptionInfo` wrapper to capture the exception, then performs type-checking (via `isinstance`) and pattern-matching. It also enforces scope rules: the exception must occur **within** the `with` block; any code after the final raising statement but still inside the block will *not* execute (due to the context manager’s semantics) and thus must be placed carefully.
Because subclasses of the expected exception class are accepted, using too generic a type (e.g., `Exception`) may hide unrelated failures.
When the legacy function form is used, pytest executes the function and its args internally, then performs equivalent checks.

In terms of reporting, if the exception is correct the test shows a pass; if not, pytest prints a failure message describing either "DID NOT RAISE <ExceptionType>" (if no exception) or "DID NOT MATCH <pattern>" (if `match` failed) or a custom message (in older versions) and shows the actual raised exception type.

---

## Typical Use Cases

Here are common scenarios where `pytest.raises` is applied:

* Verifying that input validation logic correctly fails: e.g., a function should raise `ValueError` when given invalid input.
* Ensuring that resource cleanup or error conditions trigger designed exception paths (e.g., `FileNotFoundError`, `PermissionError`).
* Asserting correct exception message content beyond type matching (via `match`) when message semantics matter.
* Using `check` to validate additional properties of a raised exception (e.g., specific `.errno` attribute).
* In combination with `pytest.mark.parametrize`, some test cases expect an exception and others expect normal output; using `pytest.raises` lets you embed the exception-assertion branch cleanly.
* Explicitly documenting expected failures (though this is more commonly done via `pytest.mark.xfail`) by asserting that specific behaviour should still raise a known exception.

---

## Example

```python
import pytest

def divide(a, b):
    if b == 0:
        raise ZeroDivisionError("division by zero is undefined")
    return a / b

def test_divide_by_zero():
    with pytest.raises(ZeroDivisionError, match="division by zero"):
        divide(10, 0)

def test_divide_normal():
    assert divide(10, 2) == 5.0

def test_exception_attributes():
    with pytest.raises(OSError, check=lambda e: e.errno == 2):
        raise OSError(2, "No such file or directory")
```

*Expected output:*
The first test passes because `divide(10,0)` raises `ZeroDivisionError` with matching message.
The third test passes only if the `OSError` raised has `errno == 2`; otherwise pytest reports a failure.

---

## Best Practices and Pitfalls

* **Prefer the context-manager form** (`with pytest.raises(...)`) over the legacy function form; the latter is supported but less readable and more error-prone.
* **Do not use overly broad exception types** (e.g., `Exception`) unless intentionally catching any exception; doing so may mask genuine bugs.
* **Ensure the code that raises lies immediately within the `with` block**, and avoid adding extra statements inside the block after the exception-raising line, because they will not execute if the exception occurs earlier.
* If you supply `match`, verify that your regex matches the exact or intended portion of the exception message and escape special characters if necessary.
* Use `check` only when you need to inspect non-type attributes of the exception; overuse can complicate test readability.
* When using `pytest.mark.parametrize`, combine normal and exception cases cleanly rather than mixing inside one test body awkwardly.
* If you rely on `ExceptionInfo` returned via `as excinfo`, be aware of resource management: large tracebacks may cause slow tests or memory pressure if not cleared promptly.
* Avoid legacy usage where you specify a callable and arguments directly (`pytest.raises(Type, f, args…)`) in new code; it is considered dated.
* When expecting grouped exceptions (`ExceptionGroup`) use the newer `pytest.RaisesGroup` rather than `pytest.raises`.

---
