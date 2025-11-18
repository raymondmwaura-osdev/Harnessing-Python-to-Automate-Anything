# Custom Fixtures

In pytest, a *fixture* is a function decorated with `@pytest.fixture` that establishes a test context: it allocates resources, configures state, or sets up dependencies, and then yields or returns objects that tests can use.

When a test function declares a fixture in its parameter list, pytest recognizes that dependency, executes the corresponding fixture function, captures its return value, and passes that value into the test as an argument.

This design makes fixtures explicit, modular, and composable: fixtures can depend on other fixtures, and they can be parametrized or scoped.

---

## How Tests Access Variables from Fixtures

Here is a breakdown of the mechanism by which tests access fixture-provided variables, and the patterns that arise.

### Basic Fixture Return and Injection

1. **Define a fixture**:

   ```python
   import pytest

   @pytest.fixture
   def sample_value():
       # Setup logic (if any)
       return 42
   ```

2. **Use the fixture in a test**:

   ```python
   def test_using_sample(sample_value):
       # sample_value is directly available to this test
       assert sample_value == 42
   ```

   * Pytest inspects the test signature, sees `sample_value`, finds the fixture of the same name, runs it, and injects its result into the test.
   * The test is therefore receiving a *variable* (in this case, an integer) defined by the fixture.

---

### Fixtures Depending on Other Fixtures

Fixtures themselves can declare dependencies on other fixtures. This allows you to build layered or hierarchical test contexts.

**Example**:

```python
@pytest.fixture
def base_list():
    return []

@pytest.fixture
def prepared_list(base_list):
    base_list.extend([1, 2, 3])
    return base_list

def test_prepared(prepared_list):
    assert prepared_list == [1, 2, 3]
```

* `prepared_list` depends on `base_list`.
* When `test_prepared` is run, pytest will execute `base_list`, then `prepared_list`, and then pass the return of `prepared_list` into the test.
* The test does not need to know anything about how `prepared_list` was built; it simply uses the final object.

---

### Returning Multiple Variables from a Fixture

Sometimes you want a fixture to provide more than one object or piece of data. You can package them together (e.g., as a tuple, a dict, or a custom object) and return that from the fixture.

**Example**:

```python
@pytest.fixture
def data_and_meta():
    data = {"user": "alice", "id": 123}
    metadata = {"source": "fixture", "version": 1.0}
    return data, metadata

def test_data_and_meta(data_and_meta):
    data, metadata = data_and_meta
    assert data["user"] == "alice"
    assert metadata["source"] == "fixture"
```

This pattern is widely used to expose structured context: the test simply unpacks whatever the fixture returns.

---

### Fixture Scope and Caching

Fixtures can be scoped at different levels: `function` (the default), `class`, `module`, `package`, or `session`.

* **Function scope**: The fixture is run once per test function.
* **Module scope**: The fixture is run once per module, and the same returned object is shared among tests in that module.
* Pytest also caches the fixture result within a test (or within its scope), so if a test or other fixture requests the same fixture multiple times, the fixture is not re-executed each time.

That caching ensures that the test receives a consistent object and prevents redundant setup.

---

### Autouse Fixtures

Pytest supports *autouse fixtures*: these are fixtures that apply to tests automatically, without being explicitly declared in the test signature.

* You mark a fixture with `@pytest.fixture(autouse=True)`.
* Such a fixture runs before each test (or according to its defined scope).
* Even though tests don’t explicitly request it, they still “get” the side-effects or returned context of that fixture.

However, for a test to directly access the return value of an autouse fixture, it still needs to accept it as a parameter. Autouse just means the fixture is *executed*, not that its value is automatically passed into every test.

---

### Finalization / Cleanup via Fixtures

Fixtures can define teardown logic using `yield` or `addfinalizer`. The pattern is:

```python
@pytest.fixture
def managed_resource():
    resource = acquire_resource()
    yield resource
    # cleanup after the test
    release_resource(resource)
```

* Code before the `yield` is setup.
* The value yielded is what the test receives.
* Code after the `yield` is teardown, executed after the test (or after all tests, depending on scope).
* Because the test uses the same “resource” object returned by the fixture, it can inspect or mutate it, and then you can clean up predictably.

---

### Parametrized Fixtures

Fixtures can be parametrized so that pytest runs dependent tests multiple times with different fixture values.

Example:

```python
@pytest.fixture(params=[0, 1, 2])
def number(request):
    return request.param

def test_number(number):
    assert number in {0, 1, 2}
```

* Here, the fixture `number` is called three times (once for each parameter).
* Tests that use `number` will run three times, once for each value.
* Each test invocation receives a different `number` value.

---

### Advanced: Factory Fixtures

Sometimes you don’t want the fixture to return a static object, but instead a *factory function* that the test can call to get multiple instances or different configurations.

Example:

```python
@pytest.fixture
def make_record():
    created = []
    def _factory(name):
        record = {"name": name, "data": []}
        created.append(record)
        return record
    yield _factory
    # Optionally clean up created records here if needed
```

* The test receives `make_record`, a callable.
* The test can call `make_record("Alice")` or `make_record("Bob")` to create different contexts.
* The fixture handles tracking those created objects, potentially cleaning them up after the test.

---

## Best Practices and Considerations

* **Clarity**: Name fixtures clearly to reflect what they provide (data, resource, factory).
* **Modularity**: Build small, reusable fixtures rather than monolithic ones. Fixtures can depend on other fixtures.
* **Scope carefully**: Use broader scopes (module/session) only for expensive or shared resources; smaller scopes (function) give isolation.
* **Avoid over-patching**: If using mocks or patches inside fixtures, ensure tests remain readable and maintainable. (You can pass the mock object out of the fixture to the test, if needed.)
* **Yield-based fixture teardown**: Use `yield` in fixtures when you need deterministic cleanup.
* **Parametrization**: Parametrize fixtures for coverage of multiple scenarios without duplicating test code.

---
