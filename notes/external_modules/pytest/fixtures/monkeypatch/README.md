# `monkeypatch`

## Concept and Rationale

In unit testing, one often encounters code that depends on external state or side-effects; for example: network calls, file system access, environment variables or global configuration. If left un-controlled, those dependencies make tests brittle, slow or non-deterministic.
The `monkeypatch` fixture offers a controlled mechanism to **temporarily override** attributes, functions, environment variables, dictionary entries, import paths etc., during the scope of a test. Importantly, after the test finishes, `monkeypatch` ensures your changes are reverted; helping maintain test isolation.  
Rather than directly modifying production code or relying only on injection, `monkeypatch` gives you runtime flexibility to steer behaviour for the purposes of testing.

---

## Core API Methods

Key methods provided by `monkeypatch` include:

* `monkeypatch.setattr(obj, name, value, raising=True)`; set attribute `name` on object/module `obj` to `value`.
* `monkeypatch.delattr(obj, name, raising=True)`; delete attribute `name`.
* `monkeypatch.setitem(mapping, name, value)`; set `mapping[name] = value`.
* `monkeypatch.delitem(mapping, name, raising=True)`; delete `mapping[name]`.
* `monkeypatch.setenv(name, value, prepend=None)`; set environment variable `name` to `value`.
* `monkeypatch.delenv(name, raising=True)`; delete environment variable `name`.
* `monkeypatch.syspath_prepend(path)`; prepend `path` to `sys.path`.
* `monkeypatch.chdir(path)`; change current working directory for the test.
* `monkeypatch.context()`; create a context manager for grouping patches.

Each of these modifications is automatically undone after the test completes.

---

## Fundamental Examples

### Patch a function or method

**Scenario**: A function uses `Path.home()` to compute a path. In tests you don’t want location to vary by OS/user.

```python
# in module: app.py
from pathlib import Path

def get_ssh_path():
    return Path.home() / ".ssh"

# in test file: test_app.py
from pathlib import Path
import app

def test_get_ssh_path(monkeypatch):
    def fake_home():
        return Path("/tmp/testhome")
    monkeypatch.setattr(Path, "home", fake_home)

    result = app.get_ssh_path()
    assert result == Path("/tmp/testhome/.ssh")
```

Here the `Path.home()` call is overridden for the test duration, so the behaviour is deterministic.

### Patch returned objects / simulate external dependencies

**Scenario**: A function calls `requests.get(url)` and then uses `response.json()`. In a unit-test we don’t want a live HTTP call.

```python
# in module: api_client.py
import requests

def fetch_data(url):
    response = requests.get(url)
    return response.json()

# in test: test_api_client.py
import api_client
import requests

class DummyResponse:
    def json(self):
        return {"key": "value"}

def test_fetch_data(monkeypatch):
    def fake_get(*args, **kwargs):
        return DummyResponse()

    monkeypatch.setattr(requests, "get", fake_get)
    result = api_client.fetch_data("https://irrelevant")
    assert result == {"key": "value"}
```

Here we replace `requests.get` so that no network call occurs and the behaviour is controlled.

### Modify environment variables

**Scenario**: Some code reads an environment variable to adjust behaviour. You want to test both “present” and “absent” cases.

```python
# in module: config.py
import os

def get_app_mode():
    mode = os.getenv("APP_MODE")
    if mode is None:
        raise RuntimeError("APP_MODE not set")
    return mode.lower()

# in test: test_config.py
import config
import pytest

def test_app_mode_set(monkeypatch):
    monkeypatch.setenv("APP_MODE", "Production")
    assert config.get_app_mode() == "production"

def test_app_mode_missing(monkeypatch):
    monkeypatch.delenv("APP_MODE", raising=False)
    with pytest.raises(RuntimeError):
        config.get_app_mode()
```

This use-case demonstrates how environment-based behaviours can be exercised in isolation.

---

## Best Practices and Caveats

### Use it for external dependencies, not trivial logic

While `monkeypatch` is powerful, over-use can lead to brittle tests which mirror implementation details rather than behaviour. The underlying article cautions that patching too deep into implementation may lead to maintenance issues.

### Patch at the correct namespace

A common pitfall is patching the wrong object or wrong namespace. Because Python modules cache imports and attribute look-ups are by reference, you must ensure you patch **where** the code under test uses it. See discussion on Stack-Overflow.

### Keep patches scoped to the test

Since `monkeypatch` will undo changes automatically at test-end, avoid having global tests rely on changed behaviour. Use fixtures or context managers if you need setup across multiple tests.

### Avoid patching core built-in functions unless necessary

Modifying built-in behaviour (for example `open`, `compile`) may interfere with underlying test runner internals (like pytest itself). Use caution or isolate patches via `monkeypatch.context()`.

---
