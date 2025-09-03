# `default`

## Key Topics

- [Overview](#overview)
- [Allowed Values](#allowed-values)
- [Examples](#examples)
- [Interaction With Other Parameters](#interaction-with-other-parameters)
- [Best Practices](#best-practices)
- [Common Pitfalls](#common-pitfalls)

---

## Overview

+ The `default` parameter defines what value an argument will hold when the user omits that argument on the command line.
+ It exists to ensure that `args.<name>` always has a well-defined value; improving program reliability and reducing the need for extra checks.
+ By default, if `default` is not explicitly provided, optional arguments receive `None` (unless overridden by global defaults or special settings like `argparse.SUPPRESS`).

---

## Allowed Values

The `default` parameter can accept **any** Python object, including but not limited to:

- Primitive types (`int`, `str`, `float`, etc.)
- Collections (`list`, `dict`)
- Callables (although the function is evaluated at `add_argument` time, not deferentially)
- Special constants such as `argparse.SUPPRESS` to avoid creating the attribute in the parsed namespace

---

## Examples

### Example 1: Default string

```python
import argparse
parser = argparse.ArgumentParser()
parser.add_argument('--name', default='Anonymous', help='Your name')
args = parser.parse_args([])
print(args)
````

**Output and Explanation**

```bash
$ python script.py
Namespace(name='Anonymous')
```

Because `--name` was omitted, `args.name` becomes `"Anonymous"`.

---

### Example 2: Default with `nargs` for optional positional

```python
import argparse
parser = argparse.ArgumentParser()
parser.add_argument('path', nargs='?', default='.', help='Target path')
args = parser.parse_args([])
print(args)
```

**Output and Explanation**

```bash
$ python script.py
Namespace(path='.')
```

Here `nargs='?'` allows zero or one positional argument; when omitted, `path` defaults to `"."`, often representing current directory.

---

### Example 3: Suppressing attribute when omitted

```python
import argparse
from argparse import SUPPRESS
parser = argparse.ArgumentParser(argument_default=SUPPRESS)
parser.add_argument('--opt', help='Optional', default='X')
args = parser.parse_args([])
print(vars(args))
```

**Output and Explanation**

```bash
$ python script.py
{}
```

Using `SUPPRESS` as `argument_default`, arguments not provided are not added to the namespace; thus `args.opt` is absent.

---

## Interaction With Other Parameters

The `default` parameter interacts with:

* **`nargs`**: For some values like `'?'`, `default` provides fallback when the argument is omitted entirely but not when the flag is present without accompanying value (see `const` for that case).
* **`const`**: Combined with `nargs='?'`, `const` supplies a value when the option is used without a following argument; `default` applies only when the option is omitted entirely.
* **`type`**: Any default value should be compatible with the specified `type`, as `type` may not be applied to defaults (depends on version and specifics).
* **`action`**: Some actions, such as `store_true`, inherently define defaults (`False`), but `default` can override those.
* **`argument_default`**: In the `ArgumentParser` constructor, this sets a global default; can be overridden per argument via `default`.

---

## Best Practices

* **Provide sensible defaults** to reduce boilerplate checks and improve usability.
* Use `nargs='?'` together with `default` for optional positional or option values.
* Use `argparse.SUPPRESS` when you want attributes only present if explicitly provided.
* Choose defaults with compatible types, and ensure help message communicates the default (for clarity).
* To display defaults in the help text automatically, use `ArgumentDefaultsHelpFormatter` or a custom formatter.

---

## Common Pitfalls

* **Default functions evaluated too early**: Providing a function call as default will run at parser definition (not at runtime parsing) potentially causing unwanted side effects.
* **Confusion between `default` and `const`**: With `nargs='?'`, `const` applies when the flag is used without a value; `default` applies when the flag is entirely omitted.
* **Attribute always exists**: Even if omitted, optional argument attributes appear as `None` (unless suppressed), which may lead to subtle errors.
* **Overriding type conversion**: Defaults aren’t necessarily passed through `type`, so type mismatches may occur.

---
