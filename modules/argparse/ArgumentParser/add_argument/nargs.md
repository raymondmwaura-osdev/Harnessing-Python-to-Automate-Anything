# `nargs`

`nargs` controls how many command-line tokens a single argument consumes when parsing. By default, an argument consumes exactly one token. Setting `nargs` alters that contract so an argument can accept zero, one, many, or an exact count of values, with the result placed into the parsed `Namespace`.

# Allowed values and their behavior

## a) An integer `N`

Consume exactly `N` values and store them as a list. If fewer than `N` values are present, parsing fails.
Note that `nargs=1` produces a single-element list (e.g. `["single"]`), which is different from the default `nargs=None`, where you get a bare value (e.g. `"single"`).

### Example

```python
# nargs_int.py
import argparse
p = argparse.ArgumentParser()
p.add_argument("coords", nargs=3, type=int)
args = p.parse_args()
print(args)
```

### Output and Explanation

```bash
$ python nargs_int.py 1 2 3
Namespace(coords=[1, 2, 3])
```

+ `nargs=3` consumes exactly three arguments. Stored as a list.

```bash
$ python nargs_int.py 1 2
usage: nargs_int.py [-h] coords coords coords
nargs_int.py: error: the following arguments are required: coords
```

+ Fewer than 3 values cause an error.

---

## b) `'?'` (zero or one)

Consume one value if available. If absent:

* For **positionals**, `default` is used.
* For **optionals**, there is a second case: if the flag appears without a value, `const` is used, otherwise if the flag is missing entirely, `default` is used.

### Example

```python
# nargs_question.py
import argparse
p = argparse.ArgumentParser()
p.add_argument("maybe", nargs="?", default="DEFAULT")
p.add_argument("--flag", nargs="?", const="CONST", default="DEFAULT")
args = p.parse_args()
print(args)
```

### Output and Explanation

```bash
$ python nargs_question.py
Namespace(maybe='DEFAULT', flag='DEFAULT')
```

+ Since the positional argument `maybe` is missing, the `default` is used.
+ Since the optional argument `flag` is entirely missing, the `default` is used.

```bash
$ python nargs_question.py value
Namespace(maybe='value', flag='DEFAULT')
```

+ Positional took the value given.
+ Optional is still entirely missing, the `default` is used.

```bash
$ python nargs_question.py --flag
Namespace(maybe='DEFAULT', flag='CONST')
```

+ Optional is present without a value. Therefore, `const` is used.

```bash
$ python nargs_question.py --flag custom
Namespace(maybe='DEFAULT', flag='custom')
```

+ Optional is present with a value. Therefore, the value is used.

---

## c) `'*'` (zero or more)

Consume all following non-option tokens into a list. It rarely makes sense to have more than one positional with `nargs='*'`, since the first one would greedily absorb everything. Multiple optionals can safely use `'*'`.

### Example

```python
# nargs_star.py
import argparse
p = argparse.ArgumentParser()
p.add_argument("items", nargs="*")
args = p.parse_args()
print(args)
```

### Output and Explanation

```bash
$ python nargs_star.py
Namespace(items=[])
```

+ Zero arguments are allowed. An empty list is used.

```bash
$ python nargs_star.py a b c
Namespace(items=['a', 'b', 'c'])
```

+ All following values get captured in the list.

---

## d) `'+'` (one or more)

Like `'*'`, but requires at least one value. If none is provided, parsing errors.

### Example

```python
# nargs_plus.py
import argparse
p = argparse.ArgumentParser()
p.add_argument("files", nargs="+")
args = p.parse_args()
print(args)
```

### Output and Explanation

```bash
$ python nargs_plus.py file1 file2
Namespace(files=['file1', 'file2'])
```

+ All following values get captured in the list.

```bash
$ python nargs_plus.py
usage: nargs_plus.py [-h] files [files ...]
nargs_plus.py: error: the following arguments are required: files
```

+ Requires at least one argument. An error is raised if missing.

---

## e) `argparse.REMAINDER` (capture “the rest”)

Gathers the remaining tokens, including ones that look like options, into a list. This is handy for wrapper commands that forward arguments to another tool.

### Example

```python
# nargs_remainder.py
import argparse
import argparse
p = argparse.ArgumentParser()
p.add_argument("cmd", nargs=argparse.REMAINDER)
args = p.parse_args()
print(args)
```

### Output and Explanation

```bash
$ python nargs_remainder.py echo hello world
Namespace(cmd=['echo', 'hello', 'world'])
```

+ Consumes all remaining arguments.

```bash
$ python nargs_remainder.py --option --flag
Namespace(cmd=['--option', '--flag'])
```

+ Even arguments that look like options are taken as plain strings.

---

# Interactions with other `add_argument` parameters

### a) `default`

Used when an argument is optional and missing, or when a positional with `nargs='?'` or `'*'` is not provided. If `default=argparse.SUPPRESS`, no attribute is added to the `Namespace` when the argument is absent.

### b) `const`

Relevant primarily for optionals with `nargs='?'`: if the flag is present with no following value, `const` is stored. In Python 3.11 and later, `const` defaults to `None`.

### c) `action`

If you do not specify `nargs`, the action determines consumption count. If you set `nargs`, your value overrides the default of the action.

### d) `type`

Applied to each consumed token. For `N`, `'*'`, and `'+'`, conversion happens element-wise. If `default` is a string, `type` is applied to it as well.

---

## Common Pitfalls

1. **Expecting a scalar with `nargs=1`**
   You will get a list of length one. If you want a scalar, leave `nargs` unset.

2. **Stacking multiple greedy positionals**
   Placing two positionals that both use `'*'` or `'+'` is ambiguous. Prefer a single greedy positional, add `--` separators, or refactor the interface.

3. **Confusing `default` and `const` with `'?'`**
   For optionals with `'?'`:

    * flag entirely missing uses `default`,
    * flag present without value uses `const`,
    * flag present with value uses that value.

4. **Forgetting that `type` applies element-wise**
   For multi-value arguments, `type` converts each token. If your `default` is a string, it is also converted.

---
