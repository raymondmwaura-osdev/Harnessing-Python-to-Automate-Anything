# `deprecated`

## Key Topics

* [Overview](#overview)
* [Allowed Values](#allowed-values)

  * [`True` or `False`](#true-or-false)
* [Examples](#examples)
* [Interaction With Other Parameters](#interaction-with-other-parameters)
* [Best Practices](#best-practices)
* [Common Pitfalls](#common-pitfalls)

---

## Overview

The `deprecated` parameter enables developers to mark a command-line argument as obsolete. When an argument is marked with `deprecated=True`, its usage triggers a warning printed to `sys.stderr`, alerting users that the option is deprecated and will be removed in future versions. This mechanism facilitates gradual deprecation, enabling backward compatibility while encouraging migration. Added in Python 3.13.0.

---

## Allowed Values

### `True` or `False`

* When `deprecated=True` is specified, and the user invokes the argument, a warning is output to `sys.stderr` in the format:

  ```
  <prog>: warning: option '<argument>' is deprecated
  ```

* When `deprecated=False`, no warning is produced and the argument behaves as usual.
* The parameter applies to both optional arguments (flags) and subcommands (via `add_parser`).

#### Example

```python
import argparse
parser = argparse.ArgumentParser(prog='tool.py')
parser.add_argument('--legacy', type=int, default=0, deprecated=True)
args = parser.parse_args()
print(args)
```

#### Output and Explanation

```bash
$ python tool.py --legacy 5
tool.py: warning: option '--legacy' is deprecated
Namespace(legacy=5)
```

This example demonstrates that `--legacy` triggers a deprecation warning when used, while still setting the parsed value in the `Namespace`.

---

## Examples

1. **Deprecating an optional flag**

   ```python
   parser.add_argument('-d', '--debug', action='store_true', deprecated=True,
                       help='Enable debug mode (deprecated)')
   ```

   Should the user supply `--debug`, a warning will be printed while the flag remains functional.

2. **Deprecating a positional argument within subparser**

   ```python
   sub = parser.add_subparsers().add_parser('oldmode', deprecated=True)
   sub.add_argument('input_file')
   ```

   Using the `oldmode` subcommand emits a warning for deprecation, while maintaining compatibility with older workflows.

3. **Gracefully phasing out a subcommand**

   ```python
   sub = parser.add_subparsers()
   old_cmd = sub.add_parser('remove-old-feature', deprecated=True,
                             help='Deprecated command, will be removed')
   old_cmd.add_argument('--param', type=str)
   ```

   Using `remove-old-feature` produces a deprecation warning prior to execution of the subcommand.

---

## Interaction With Other Parameters

* **`help`**: Although not technically necessary, it's advisable to annotate deprecated arguments in the help message, for example: `help='... (deprecated)'`, to increase transparency.
* **`default`, `type`, `choices`, `required`, `action`, etc.**: These parameters operate normally. Deprecation does not affect behavior beyond warning emission.
* **`add_parser(..., deprecated=True)`**: Supports deprecation of subparsers (subcommands) as well.

---

## Best Practices

* Use `deprecated=True` to phase out obsolete arguments while preserving backward compatibility.
* Reflect deprecation status in `help` messages for clarity to users.
* Establish a deprecation timeline in documentation, clearly stating when the parameter will be removed.
* Consider implementing alternative argument(s) alongside the deprecated one, guiding users toward current best practices before removal.

---

## Common Pitfalls

* **Assuming deprecated arguments are disabled**: `deprecated=True` does not disable or remove functionality; it only provides a warning.
* **Neglecting documentation**: Omitting mention of deprecation in help messages can confuse users despite the stderr warning.
* **Failure to plan removal**: Merely marking an argument deprecated without scheduling its removal may result in bloated or outdated interfaces.
* **Overuse**: Excessive use of deprecation can overwhelm users with warnings; this should be reserved for genuinely obsolete parameters.

---
