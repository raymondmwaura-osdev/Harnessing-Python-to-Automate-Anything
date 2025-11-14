# `monkeypatch.setattr`

## Overview

The method `setattr` of the `monkeypatch` fixture (from pytest) allows a test to temporarily replace an attribute (such as a function, method or class attribute) on a module, class or object for the duration of a test. After the test completes, the original attribute is restored automatically. Its primary purpose is to facilitate isolation in unit tests by controlling side-effects and dependencies (for example, file system access, network calls or global state) without altering production code.
In typical use-cases, you override or mock a dependency that the code under test uses such that the test remains deterministic. The behaviour of `setattr` interacts with Python’s attribute model and also supports specifying a *dotted import path* (string) instead of a direct object reference, thus accommodating modules imported in different ways.

---

## Syntax

```python
monkeypatch.setattr(target, name=None, value=<required>, raising=True)
```

or when using string import path:

```python
monkeypatch.setattr("module.submodule.ClassOrFunc", value, raising=True)
```

---

## Parameters

* **target**:

  * **Type:** `object` or `str`
  * **Description:** The object (e.g., module or class) whose attribute you wish to set. If given as a string, it must represent a dotted import path (module + attribute name).
  * **Default Value:** None (but you must provide either the target (object) with `name`, or a string path)
  * **Constraints:** If a string path is used, the string must contain at least one dot `.` separating module path and attribute name. If the path is invalid, a `TypeError` or `ImportError` may occur.

* **name**:

  * **Type:** `str` or `None`
  * **Description:** The name of the attribute on `target` to override. If `target` is a string import path, `name` is omitted and the last part of the dotted path is taken as attribute name.
  * **Default Value:** `None` (only valid when `target` is a string path)
  * **Constraints:** If `target` is an object (not string) then `name` must be provided and must refer to an existing attribute (unless `raising=False` is used).

* **value**:

  * **Type:** any Python object (callable or not)
  * **Description:** The replacement value you want the attribute to have during the test. For example, you might substitute a function with a dummy function or a class attribute with a stub.
  * **Default Value:** None (you must provide)
  * **Constraints:** Must be assignment-compatible (e.g., if attribute was used as function, replacing it with non‐callable may lead to errors when code under test invokes it).

* **raising**:

  * **Type:** `bool`
  * **Description:** Indicates whether `setattr` should raise an exception if the attribute does **not** already exist on the target. If `raising=True`, `AttributeError` (or relevant exception) is raised for missing attribute; if `raising=False`, then the attribute will be created/assigned regardless.
  * **Default Value:** `True`
  * **Constraints:** Use of `raising=False` can allow injecting attributes that didn’t exist; but doing so should be done with care, as it may change semantics of test significantly.

---

## Return Value

* **Type:** `None`
* **Description:** `setattr` does **not** return a value. It simply performs the patch. The underlying fixture tracks the change so that when the test ends (or when `undo()` is called), the original attribute value is restored automatically.
* **Special Notes:** Because the return value is `None`, you should not expect a value from the call; you verify behaviour of the code under test (after the patch) rather than from the patch call itself.

---

## Errors and Exceptions

* **`TypeError`**

  * **Cause:** When the `target` parameter is a string that does not include a dot (i.e., not a valid import path), or is of an unexpected type.
  * **Example:**

    ```python
    monkeypatch.setattr("invalidpath", lambda: None)  # raises TypeError
    ```

* **`ImportError`**

  * **Cause:** When the string import path cannot be resolved; the module part cannot be imported.
  * **Example:**

    ```python
    monkeypatch.setattr("nonexistent.module.func", lambda: None)  # raises ImportError
    ```

* **`AttributeError`**

  * **Cause:** When `raising=True` (default) and the object does not already have the attribute specified in `name`.
  * **Example:**

    ```python
    class A: pass
    monkeypatch.setattr(A, "missing_attr", 123)  # raises AttributeError
    ```

* **Potential misuse / impairment**
  Patch of very core built-in functions or modules used by pytest itself may break pytest; documentation warns specifically. ([pytest][3])

---

## Example

```python
# Example 1: Patch a function on a class
# module: mymodule.py
class UserService:
    def get_current_user(self):
        # Suppose this calls out to authentication service
        ...

# test_mymodule.py
def test_user_service(monkeypatch):
    # Replace get_current_user to always return a dummy user
    monkeypatch.setattr(UserService, "get_current_user", lambda self: "dummy_user")
    service = UserService()
    assert service.get_current_user() == "dummy_user"
```

```python
# Example 2: Using dotted import path
# module: config.py
import os

def get_mode():
    return os.getenv("MODE", "default")

# test_config.py
def test_get_mode(monkeypatch):
    # Patch os.getenv via string path
    monkeypatch.setattr("os.getenv", lambda *args: "TEST_MODE")
    assert get_mode() == "TEST_MODE"
```

```python
# Example 3: Suppress AttributeError by creating attribute
class A: pass

def test_add_attribute(monkeypatch):
    # The attribute 'new_attr' does not exist; allow creation
    monkeypatch.setattr(A, "new_attr", 456, raising=False)
    assert A.new_attr == 456
```

---

## Best Practices

* Use `monkeypatch.setattr` **only** when you need to override behaviour that cannot be easily injected via parameters or dependency injection; prefer design that supports injection for testability.
* Ensure you patch the **correct namespace**: patch the attribute where the code under test **looks it up**, not necessarily where it was originally defined. Namespace confusion is a leading cause of ineffective patches.
* Keep patches as narrow in scope as possible: use fixtures or context‐managers (`monkeypatch.context()`) when doing more complex patching so teardown is well controlled.
* Avoid patching Python built-ins or modules that may interfere with pytest internals (e.g., `open`, `compile`, `sys.modules`).
* For readability, always annotate in test code what you’re overriding and why; this improves maintainability for other developers.
* Use version-compatible code: newer versions of pytest may refine behaviour of `monkeypatch`, but the API of `setattr(target, name, value, raising)` remains stable as of pytest 6-7.

---

## Common Pitfalls

* **Patching the wrong object or namespace**: If code under test imported a function or class via `from module import func`, patching `module.func` may not affect the imported reference; you must patch where it's used.
* **Expecting return from `setattr`**: The call returns `None`; test must assert behaviour of the patched code, not the patch call.
* **Leaving side-effects across tests**: While pytest undoes patches automatically at test teardown, manual use (e.g., via `MonkeyPatch()` directly) must call `undo()` to avoid bleed-over.
* **Setting `raising=True` when attribute missing**: This will cause an `AttributeError`; if you intend to create a new attribute, use `raising=False`.
* **Patching too deeply/ubiquitously**: Overusing patching can lead to tests tightly coupled to implementation rather than behaviour, making refactorings painful.
* **Patch timing issues**: The patch must be applied *before* code under test executes its lookup; patching after the code runs is ineffective.

---
