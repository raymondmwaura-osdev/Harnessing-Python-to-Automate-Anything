# `add_mutually_exclusive_group()`

## Introduction

The method `add_mutually_exclusive_group()` is defined on the class ArgumentParser. It is used to create an *argument group* within a command-line parser in which the arguments are **mutually exclusive**. In other words, if you add more than one argument into such a group, the parser will enforce that *at most one* of them may be present in any given invocation of the program. This is useful for modelling options like “you may specify `--foo` or `--bar`, but not both”.

Example:

```python
parser = argparse.ArgumentParser(prog='PROG')
group = parser.add_mutually_exclusive_group()
group.add_argument('--foo', action='store_true')
group.add_argument('--bar', action='store_true')
```

If the user runs `PROG --foo --bar`, the parser produces an error: “argument --bar: not allowed with argument --foo”.
Thus the method helps enforce logical constraints among options, improving CLI usability and correctness.

---

## Parameters

The `add_mutually_exclusive_group()` method supports the following parameters:

+ `required`: `bool`, optional, default `False`: If set to `True`, the group becomes *required*: the parser will enforce that *at least one* of the arguments in the group must be present in the invocation. If `required=False` (the default), then zero or one of the group’s arguments may appear.

Note: According to the official documentation, those are the only supported parameters for this method.
Some external discussions point out that the mutually exclusive group *does not* support `title` or `description` keyword arguments (unlike `add_argument_group()`) for customizing help-group labels.

---

## Return Value

The method `add_mutually_exclusive_group()` returns an instance of the class MutuallyExclusiveGroup (internal to `argparse`). This returned object has its own `add_argument()` method, so you can attach arguments to the group.
In effect:

```python
mutex_group = parser.add_mutually_exclusive_group(required=True)
# mutex_group is the object returned
mutex_group.add_argument('--foo', help='...')
mutex_group.add_argument('--bar', help='...')
```

---

## Examples

### Example 1: Basic mutually exclusive flags

```python
import argparse

parser = argparse.ArgumentParser(prog='demo')
group = parser.add_mutually_exclusive_group()
group.add_argument('--verbose', action='store_true', help='enable verbose output')
group.add_argument('--quiet',   action='store_true', help='suppress output')

args = parser.parse_args()
if args.verbose:
    print("Verbose mode")
elif args.quiet:
    print("Quiet mode")
else:
    print("Normal mode")
```

**Behaviour**: Running `demo.py --verbose --quiet` will result in an error:

```
usage: demo.py [-h] [--verbose | --quiet]
demo.py: error: argument --quiet: not allowed with argument --verbose
```

The “`|`” character in the usage means “either/or”.

### Example 2: Required mutually exclusive group

```python
import argparse

parser = argparse.ArgumentParser(prog='choose')
group = parser.add_mutually_exclusive_group(required=True)
group.add_argument('-a', '--add',    action='store_true', help='add item')
group.add_argument('-r', '--remove', action='store_true', help='remove item')

args = parser.parse_args()
if args.add:
    print("Adding item")
elif args.remove:
    print("Removing item")
```

**Behaviour**: If user runs `choose.py` with neither `--add` nor `--remove`, the parser will produce an error:

```
usage: choose.py [-h] (--add | --remove)
choose.py: error: one of the arguments --add --remove is required
```

This shows how the `required=True` parameter forces *one of* the options to be present. ([claudiokuenzler.com][3])

### Example 3: Mutually exclusive options with values

```python
import argparse

parser = argparse.ArgumentParser(prog='input')
group = parser.add_mutually_exclusive_group()
group.add_argument('--file',    metavar='FILE', help='input from file')
group.add_argument('--stdin',   action='store_true',   help='input from standard input')

args = parser.parse_args()
if args.file:
    print(f"Reading from file {args.file}")
elif args.stdin:
    print("Reading from stdin")
else:
    print("Default input mode")
```

**Behaviour**: This enforces that the user does not supply both `--file` and `--stdin` at once. If they do, the parser will complain.

---
