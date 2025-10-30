# Parameter Name (Example: `scope`)

### Purpose

*(Define what the parameter controls or influences within pytest fixture behavior.)*
Explain its role in fixture lifecycle, invocation, or interaction with tests.

---

### Accepted Values

*(List all valid or commonly used values and describe their meaning.)*
For enumerated parameters (e.g., `scope`), specify each valid value and its practical impact.

---

### Default Value

*(Record the value pytest uses when the parameter is omitted.)*
Example: Default = `"function"`

---

### Behavioral Description

*(Explain in detail how the parameter modifies fixture execution, interaction, or dependency resolution.)*
Discuss both the internal logic (how pytest interprets it) and external behavior (what happens during testing).

---

### Typical Use Cases

*(List practical scenarios where the parameter is commonly used.)*
Examples may include optimization, readability improvement, or configuration control.

---

### Best Practices

*(Summarize recommendations or cautions.)*
Include points about maintainability, performance, and potential pitfalls.

---

### Example Usage

*(Provide a minimal illustrative code snippet showing the parameter in context.)*
Use clear and simple syntax focusing on the parameter being discussed.

```python
# Example
@pytest.fixture(scope="module")
def database_connection():
    ...
```

---
