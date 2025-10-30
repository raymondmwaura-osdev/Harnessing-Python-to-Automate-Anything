# Fixtures

> **NOTE**: In this directory, the other files discuss individual `@pytest.fixture` parameters (e.g. `scope.md` covers the `scope` parameter).

## What Are Fixtures

In pytest, a *fixture* is a function that is executed **before** (and optionally after) a test function runs, and whose result (or context) is provided to the test via dependency injection.
Fixtures are used for common setup tasks, shared test data, resource allocation, cleanup, and more; thereby promoting DRY (Don’t Repeat Yourself) test code.

The basic form:

```python
import pytest

@pytest.fixture
def my_fixture():
    # setup code
    resource = …
    yield resource
    # teardown/cleanup code
```

A test then uses this fixture simply by naming it as a parameter:

```python
def test_something(my_fixture):
    assert …  # use my_fixture inside
```

This approach ensures that setup/cleanup logic is separated from the test logic, improving clarity and maintainability.

---

## Fixture Scopes

The *scope* of a fixture defines **how often** the fixture is set up and torn down within the test run; i.e., what its lifetime is. Proper use of fixture scopes helps balance **isolation** (tests not interfering with each other) against **performance** (avoiding expensive repeated setup).

The standard scopes supported by pytest are (in increasing order of lifespan):

* `function`; the default: the fixture is invoked once per test function.
* `class`; the fixture is invoked once for all tests in a test class (methods).
* `module`; the fixture is invoked once per module (file).
* `package`; the fixture is invoked once per package (i.e., directory containing `__init__.py`); less commonly used.
* `session`; the fixture is invoked once for the entire test session (all modules).

### Scope Behaviour and Considerations

* **Function scope** (`scope="function"`) offers the highest isolation: each test gets a fresh fixture instance. This avoids shared state issues but may incur high setup/teardown cost if the fixture is expensive.
* **Class scope** allows sharing a fixture among methods of a test class: useful when multiple test methods within the same class need the same setup. But note that sharing means changes in one test might affect others.
* **Module scope** reduces the overhead further by sharing a fixture across all tests in the file. Use when the setup is expensive and tests in the module are compatible with shared state.
* **Package scope** is even broader and is used when multiple modules in a package share the same expensive setup.
* **Session scope** offers maximal performance benefit (setup once), but minimal isolation: any shared resource may be mutated by multiple tests. Reserved for situations where setup cost is significant and you’re comfortable managing shared state.

### Scope Hierarchy and Dependencies

Fixtures should only depend on fixtures of *equal or broader* scope. For example, a `module`-scoped fixture should not depend on a `function`-scoped fixture; doing so will produce a `ScopeMismatch` error.

### Example of Scope Usage

```python
import pytest

@pytest.fixture(scope="session")
def expensive_resource():
    # setup once for entire session
    res = …
    yield res
    # teardown once after all tests

@pytest.fixture(scope="module")
def module_resource():
    # setup once per module
    yield …
    # teardown once per module

@pytest.fixture(scope="function")
def fresh_resource():
    # setup per test function
    yield …
    # teardown per test
```

In this design:

* `expensive_resource` is created only once for the test run.
* `module_resource` is created once per module.
* `fresh_resource` is re-created for every test function.

---

## Shared Setup/Teardown Logic via Fixtures

Fixtures are not only for simple resource allocation: they provide a clean way to embed **setup and teardown logic** (using `yield`) and allow sharing that logic across many tests.

### Setup and Teardown with `yield`

When a fixture uses `yield`, pytest treats the code preceding the `yield` as setup, and the code following the `yield` as teardown. For example:

```python
import pytest

@pytest.fixture(scope="function")
def resource():
    # Setup
    obj = create_obj()
    yield obj
    # Teardown
    obj.cleanup()
```

Every test that takes `resource` will have the setup executed before the test, then the test body, then the teardown afterward.

### Sharing Fixtures via `conftest.py`

When many tests require the same fixtures, you can place fixture definitions in a `conftest.py` file (at the suitable directory level) rather than importing them manually in each test module. pytest auto-discovers fixtures in `conftest.py`.

For example, in `tests/conftest.py`:

```python
import pytest

@pytest.fixture(scope="module")
def common_setup():
    resource = …
    yield resource
    resource.teardown()
```

Then any test module under `tests/` directory can accept `common_setup` as a parameter.

### Best Practices for Fixtures

* Keep fixtures **focused**: one fixture should set up one logical resource or context.
* Choose the **minimal scope** needed: prefer function scope unless you have a clear reason to broaden it (e.g., expensive setup).
* Be mindful of shared state when you broaden fixture scope: tests may interfere with each other.
* Use `yield` for teardown logic instead of manual `try/finally`.
* Use `conftest.py` for fixtures shared across modules, avoid imports of fixtures across test modules.
* Avoid mixing heavy stateful fixtures with broad scopes unless you clearly manage state isolation.

---
