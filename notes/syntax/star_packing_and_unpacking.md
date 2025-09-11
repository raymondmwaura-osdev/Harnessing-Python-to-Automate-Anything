# `*` (star): iterable unpacking and starred expressions in Python

## Key Topics

* [Overview](#overview)
* [Syntax and contexts](#syntax-and-contexts)
* [Behavior and effects](#behavior-and-effects)
* [Examples](#examples)
* [Best practices](#best-practices)
* [Common pitfalls](#common-pitfalls)

---

## Overview

The single asterisk operator (`*`) in modern Python is a multipurpose language feature used for *packing* and *unpacking* iterables. Two canonical roles are:

* **Unpacking (spread)**: Expands an iterable into individual elements when calling functions or building new sequences.
* **Starred assignment (catch-all)**: In an assignment pattern, a starred target captures "the rest" of the iterable into a `list`.

These behaviors were formalized and extended by language proposals (PEP 3132 introduced extended iterable unpacking; PEP 448 added broader unpacking generalizations).

---

## Syntax and contexts

The star operator appears in several syntactic contexts; each has slightly different rules:

* **Function call / definition**

  * Call: `f(*iterable)` unpacks elements as positional args. Multiple `*` unpackings are allowed.
  * Definition: `def f(a, *args):` collects excess positional args into a tuple named `args`.

* **Tuple/list/dict/set displays (literal construction)**

  * Sequence: `[*a, *b, 3]` produces a new list combining contents of `a` and `b`.
  * Set: `{*a, *b}` flattens into a set.
  * Dict: `{**d1, **d2, 'k': v}` merges mappings; later keys override earlier ones. Multiple `*` / `**` unpackings are allowed in displays.

* **Assignment (starred target)**

  * `first, *middle, last = seq` assigns `first` and `last` to single items and `middle` to a `list` of the remaining items. At most one starred target is allowed in a single target list/tuple. The starred target always becomes a `list`.

---

## Behavior and effects

* **Starred assignment collects to a list**: The starred target receives a `list` object even if the right-hand iterable is a tuple, generator, or string. This design choice supports subsequent mutation and common patterns.
* **Multiple unpackings allowed in displays and calls**: Since Python 3.5 (PEP 448), you can use multiple `*` and `**` unpackings in calls and literals (e.g., `f(*a, *b)` or `[*a, *b]`).
* **Order and overriding semantics**: For dictionaries, later entries override earlier ones; when combining sequences, elements appear in left-to-right order of the expression.
* **Performance**: Unpacking creates new sequences (lists/tuples) when used in literal construction or assignment, so it incurs allocation cost; however, it is usually preferable for readability and idiomatic style unless profiling proves the allocation is a bottleneck.

---

## Examples

* **Function call unpacking**

```python
def add(a, b, c):
    return a + b + c

args = (1, 2, 3)
result = add(*args)  # equivalent to add(1, 2, 3)
```

* **Merging sequences (literal construction)**

```python
a = [1, 2]
b = (3, 4)
merged = [*a, *b, 5]  # [1, 2, 3, 4, 5]
```

* **Starred assignment (catch-all)**

```python
vals = [0, 1, 2, 3, 4]
first, *middle, last = vals
# first == 0
# middle == [1, 2, 3]
# last == 4
```

* **Merging mappings**

```python
d1 = {'x': 1, 'y': 2}
d2 = {'y': 3, 'z': 4}
merged = {**d1, **d2}  # {'x': 1, 'y': 3, 'z': 4}
```

* **Using generators with `*` to materialize**

```python
squares = (i*i for i in range(5))
lst = [*squares]  # converts the generator to a list [0,1,4,9,16]
```

---

## Best practices

* **Prefer explicitness for clarity**: Use starred assignment when it makes the code clearer (e.g., `head, *tail = seq`) rather than manual indexing. It expresses intent and avoids indexing errors.
* **Use `*` to build new containers idiomatically**: `[ *a, *b ]` is typically clearer and more flexible than `a + list(b)`, because `*` works with any iterable and reads as "flatten and combine." Tools like Ruff explicitly prefer `*` for collection concatenation because it is more general.
* **Normalize types if you need immutability**: Starred assignment yields a `list`. If you require an immutable sequence, convert the result to a `tuple`, e.g., `mid = tuple(*mid)` or `first, *middle, last = seq; middle = tuple(middle)`.
* **Be deliberate with large iterables**: Unpacking into a literal (e.g., `[ *big_generator ]`) will materialize the entire iterable into memory. If memory is a concern, iterate or use generator expressions without `*` to avoid creating a giant list.
* **Use multiple `*`/`**` in calls and literals when it improves readability**: PEP 448 permits multiple unpackings; use them when combining many sources into one call or container.

---

## Common pitfalls

* **At most one starred target in assignment**: Within a single unpacking target list/tuple, Python allows at most one starred expression. `a, *b, *c = seq` is a `SyntaxError`. Use nested structures or intermediate assignments instead.
* **Starred target is always a list**: Expect a list even if the source is a tuple or string. Code that assumes the same type as the input will break. Convert explicitly if you need a different type.
* **Too many (or too few) values to unpack**: If the non-starred targets do not match the available items, Python raises `ValueError: not enough values to unpack` or similar. Use a starred target to guard against variable lengths. Example: `a, b = [1]` raises; `a, *b = [1]` works.
* **Materializing huge iterables inadvertently**: Using `[*huge_gen]` or `list(huge_iterable)` will allocate memory proportional to the iterable size. For streaming/large data, iterate or process items lazily.
* **Ambiguous or unreadable patterns**: Overly complex unpacking (deep nested starred targets or many variables mixed with `*`) harms readability. Favor simple, explicit code for maintenance.

---
