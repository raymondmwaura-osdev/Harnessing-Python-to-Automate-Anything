# `dict.copy`

## Overview

The `dict.copy` method in Python creates a **shallow copy** of a dictionary. It produces a new dictionary object that contains the same key-value pairs as the original. The primary purpose is to allow you to duplicate a dictionary so that modifications to the new dictionary (e.g., adding or removing keys) do not affect the original dictionary.

However (because the copy is *shallow*) if the values in the dictionary are themselves mutable objects (e.g., lists, other dictionaries, sets), the references to those mutable objects are shared between the original and the copy. That means modifying a mutable object value (not the dictionary structure) via either the original or the copy will reflect in both.

`dict.copy` is often used when you need to preserve the original data structure intact while working on or transforming a similar-but-separate mapping. It’s also useful to avoid unintended side-effects when passing dictionaries around or before performing destructive operations (e.g., clearing, updating, deleting).

---

## Syntax

```python
new_dict = original_dict.copy()
```

---

## Return Value

* **Type:** `dict`
* **Description:** A new dictionary object containing the same key-value pairs (references) as the original dictionary.
* **Special Notes:** Because the copy is shallow, the new dictionary is independent in terms of its overall mapping structure (keys and their association), but the *values* are references to the same objects as in the original. If values are immutable, this effectively isolates original and copy. If values are mutable, modifications to those inner objects affect both dictionaries.

---

## Example

```python
# Example 1: Basic usage with immutable values
original = {'a': 1, 'b': 2, 'c': 3}
copy_dict = original.copy()
copy_dict['d'] = 4  # modify the copy
print("Original:", original)   # {'a': 1, 'b': 2, 'c': 3}
print("Copy:", copy_dict)      # {'a': 1, 'b': 2, 'c': 3, 'd': 4}

# Example 2: Shallow copy with mutable values
original = { 'x': [1, 2, 3], 'y': 99 }
copy_dict = original.copy()
copy_dict['x'].append(4)

print("Original:", original)   # {'x': [1, 2, 3, 4], 'y': 99}
print("Copy:", copy_dict)      # {'x': [1, 2, 3, 4], 'y': 99}

# Example 3: Clearing the copy does not affect the original
original = {1: 'foo', 2: 'bar'}
copy_dict = original.copy()
copy_dict.clear()

print("Original:", original)   # {1: 'foo', 2: 'bar'}
print("Copy:", copy_dict)      # {}
```

---

## Relation to Shallow vs Deep Copy

Because `dict.copy()` produces only a shallow copy, it does **not** copy nested mutable objects. For nested structures that must be fully independent, using `dict.copy()` may lead to unintended aliasing and side-effects. In such cases, a **deep copy** is appropriate (e.g., via `copy.deepcopy()`).

---
