# Method Name (e.g., `Path.mkdir()`)

## Sections

+ [Overview](#overview)
+ [Syntax, Parameters and Return Value](#syntax-parameters-and-return-value)
+ [Errors and Exceptions](#errors-and-exceptions)
+ [Examples](#examples)
+ [Best Practices](#best-practices)
+ [Common Pitfalls](#common-pitfalls)

---

## Overview

* A concise description of what the method does.
* Include its purpose within the broader context of `Path`.

---

## Syntax, Parameters, and Return Value

**Syntax**:

* Show the method syntax, e.g.:

  ```python
  def mkdir(self, mode: int = 0o777, parents: bool = False, exist_ok: bool = False) -> None
  ```

**Parameters**:

Tabulate each parameter:

+ `mode`: `int`; File permission bitmask (e.g., `0o777`)
+ `parents`: `bool`; Whether to create parent directories if missing
+ `exist_ok`: `bool`; Suppress exception if the target already exists

(Adjust accordingly for different methods.)

**Return Value**:

* Specify return type (e.g., `None`, `Path`, `bool`) or side effect.
* Note if the method returns `self` or modifies the filesystem.

---

## Errors and Exceptions

* List common exceptions: e.g., `FileExistsError`, `PermissionError`.
* Detail under which conditions they are raised.

---

## Examples

Provide minimal, clear code demonstrating typical usage:

```python
from pathlib import Path

# Example context and expected result
```

---

## Best Practices

* Highlight recommended usage patterns for reliability and clarity.
* Suggest idiomatic ways to combine `Path` methods for common workflows.

---

## Common Pitfalls

* Note frequent mistakes that may cause errors or unexpected results.
* Warn about platform-specific quirks or overlooked method behaviors.

---
