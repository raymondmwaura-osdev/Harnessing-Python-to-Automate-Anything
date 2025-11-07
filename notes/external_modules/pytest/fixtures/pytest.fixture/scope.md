# `scope`

### Purpose

The `scope` parameter in `@pytest.fixture` determines how frequently a fixture is set up and torn down during a pytest test session. It defines the *lifetime* of a fixture instance — in other words, how long the resource created by the fixture remains active and shared among tests.  

This parameter is central to managing fixture behavior because it controls the level of isolation between tests and helps optimize performance. By adjusting `scope`, developers can control whether a fixture is recreated for every test, shared across multiple tests, or reused throughout the entire test session.

---

### Accepted Values

Pytest supports five standard scope levels, which determine how long a fixture remains active:

**1. `"function"`**  
This is the default and most granular scope.  
The fixture is executed **once per test function**, meaning each test receives a fresh instance of the fixture.  
It provides maximum isolation and prevents tests from affecting one another.  

**2. `"class"`**  
The fixture is created **once per test class**, and all test methods within that class share the same fixture instance.  
This scope is useful when tests within a class depend on a shared setup but do not need complete isolation.

**3. `"module"`**  
The fixture is executed **once per module** (i.e., per test file).  
All test functions and classes defined in that file share the same fixture instance.  
This reduces setup overhead when multiple tests in a single module use the same resource.

**4. `"package"`**  
The fixture is executed **once per package**, meaning it is shared among all tests in the same package (a directory with an `__init__.py` file).  
This scope is less commonly used but can be beneficial when working with large test suites organized into packages that share costly initialization resources.

**5. `"session"`**  
The broadest scope available.  
The fixture is executed **once for the entire pytest run**, and all tests across all files share the same fixture instance.  
This is commonly used for resources that are expensive to create and safe to reuse globally, such as database connections, HTTP clients, or API tokens.

---

### Default Value

The default value of `scope` is `"function"`.  
If you do not explicitly specify the `scope` parameter, pytest will create and destroy the fixture for every individual test function that uses it.

---

### Behavioral Description

Internally, pytest uses the `scope` parameter to determine when to execute the fixture’s setup and teardown logic, as well as how to cache and reuse the fixture instance.  

When a fixture is declared, pytest checks its scope to decide:
1. **When to create it** — during function execution, class initialization, module import, package loading, or session startup.  
2. **When to destroy it** — after the corresponding lifetime ends (function return, class teardown, module completion, etc.).  

The fixture instance is *cached* within its scope. For example:
- A `function`-scoped fixture is destroyed immediately after each test.
- A `module`-scoped fixture is destroyed only when all tests in the module have finished.
- A `session`-scoped fixture persists until the entire pytest process exits.  

When one fixture depends on another (fixture chaining), pytest enforces **scope hierarchy**:  
A fixture may only depend on fixtures of **equal or broader** scope.  
For example, a `function`-scoped fixture can depend on a `module`- or `session`-scoped fixture, but not the other way around.  

If you violate this rule, pytest raises a `ScopeMismatch` error because a broader fixture cannot depend on a narrower one that may not exist at the time of evaluation.

---

### Typical Use Cases

1. **`function` scope**  
   - Lightweight setups that must be independent across tests.  
   - Examples: temporary file creation, mock objects, small data sets.

2. **`class` scope**  
   - Shared setup for related tests within a class.  
   - Examples: creating a shared in-memory database for multiple class methods.

3. **`module` scope**  
   - Commonly used for I/O or API-related tests that share configuration.  
   - Examples: initializing a web client or mock API for all tests in one file.

4. **`package` scope**  
   - Useful in larger codebases where multiple modules depend on the same stateful setup.  
   - Example: starting a service daemon used across several test modules in a package.

5. **`session` scope**  
   - Best for costly setups that should only happen once.  
   - Examples: connecting to a real database, authenticating an API client, or loading a global dataset.

---

### Best Practices

1. **Start with the narrowest scope** — Use `function` scope by default for maximum isolation unless performance requires broader sharing.  
2. **Avoid shared mutable state** — When using broader scopes like `module` or `session`, ensure that fixtures do not leak state between tests.  
3. **Combine with teardown logic** — Use the `yield` statement inside fixtures to ensure proper resource cleanup after the scope ends.  
4. **Respect scope hierarchy** — Always ensure dependent fixtures follow valid scope relationships (narrower fixtures may depend on broader ones, not vice versa).  
5. **Use `session` scope sparingly** — Overusing session-scoped fixtures can lead to hidden dependencies and make tests less reproducible.  
6. **Document fixture scope explicitly** — Especially in collaborative projects, note why a specific scope was chosen to avoid confusion.  

---

### Example Usage

```python
import pytest

# Function-scoped fixture (default)
@pytest.fixture
def temporary_file(tmp_path):
    file = tmp_path / "data.txt"
    file.write_text("sample data")
    return file

# Module-scoped fixture
@pytest.fixture(scope="module")
def db_connection():
    print("Setting up database connection")
    conn = connect_to_database()
    yield conn
    print("Closing database connection")
    conn.close()

# Session-scoped fixture
@pytest.fixture(scope="session")
def api_client():
    print("Initializing API client")
    client = create_api_client()
    yield client
    print("Tearing down API client")
    client.disconnect()

def test_read_file(temporary_file):
    assert temporary_file.read_text() == "sample data"

def test_query_database(db_connection):
    result = db_connection.query("SELECT 1")
    assert result == 1

def test_global_api_client(api_client):
    assert api_client.status() == "connected"
````

In this example:

* `temporary_file` is recreated for every test (`function` scope).
* `db_connection` is created once per module and reused in all tests in the same file.
* `api_client` is created once for the entire test session and shared across all tests globally.

---
