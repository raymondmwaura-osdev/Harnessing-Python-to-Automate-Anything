# `dict`

## What Is a Python Dictionary

A *dictionary* (type `dict`) in Python is a built-in data structure that implements **associative mapping**: it stores pairs of **keys** and **values**, where each key maps to exactly one value. You use a key to retrieve, add, or update the corresponding value.

Dictionaries are sometimes called *hash tables*, *maps*, or *associative arrays* in other programming languages.

---

## Key Characteristics

Dictionaries in Python have several defining properties:

* **Mutability**: They are mutable, meaning you can add, remove, or change key-value pairs after creation.
* **Key uniqueness**: Each key must be unique. Assigning a value to an existing key will overwrite the previous value.
* **Key immutability**: Keys must be hashable objects, which generally means they must be immutable (such as strings, numbers, or tuples of immutable elements). Mutable types like lists cannot be used as keys.
* **Order preservation**: Starting with Python 3.7, dictionaries preserve the order in which keys are inserted, which is reflected during iteration.

---

## Basic Operations

Several common operations can be performed with dictionaries:

* **Creation**: You can create dictionaries using curly braces, such as `{"name": "Alice", "age": 30}`, or by using the `dict()` constructor.
* **Access**: Values are retrieved using bracket notation (`d[key]`). If the key does not exist, this raises a `KeyError`. The `.get()` method allows safe access with an optional default.
* **Insertion and update**: Assigning a value to a key either adds the key (if new) or updates the value (if it already exists).
* **Deletion**: Keys and their values can be removed using `del d[key]` or methods such as `.pop()` and `.popitem()`.
* **Iteration**: You can loop through dictionaries by keys, values, or key-value pairs with `.keys()`, `.values()`, and `.items()` respectively.
* **Other features**: Dictionaries support methods such as `.update()` for merging and allow concise creation through dictionary comprehensions.

---

## Performance Considerations

Dictionaries are implemented using hash tables. This gives them excellent average-case performance: key lookup, insertion, and deletion generally take constant time, *O(1)*. However, performance can degrade in cases of hash collisions or during internal resizing. The efficiency of operations also depends on the cost of computing the hash of a key, though for common immutable types this cost is minimal.

---

## Use Cases

Dictionaries are a foundational structure in Python programming. They are particularly useful in scenarios such as:

* Mapping identifiers to data, for example user IDs to user information.
* Building frequency counters or lookup tables.
* Implementing caching and memoization.
* Representing structured data like configuration files or JSON objects.
* Any situation where fast access by key is preferable to positional indexing.

---
