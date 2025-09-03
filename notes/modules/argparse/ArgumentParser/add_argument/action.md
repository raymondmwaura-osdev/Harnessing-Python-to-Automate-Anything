# `action`

Determines how the parser processes the argument: store a value, toggle flags, count occurrences, etc.

# Recognized Values for `action`

## 1. `store`

* **Description**: The default action. It stores the argument’s value; typically as a string or converted type; into the `Namespace` object.
* **Usage**: No special behavior; just takes the provided value.

---

## 2. `store_const`

* **Description**: Stores a predefined constant (`const`) when the argument is encountered, regardless of any value supplied.
* **Usage**: Used in conjunction with `const`; useful for flag-like behavior with custom constant values.

### Example

```python
import argparse

parser = argparse.ArgumentParser()

# Define an argument with store_const
parser.add_argument(
    "--set-flag",
    action="store_const",
    const="FLAG_ACTIVE",
    default="FLAG_INACTIVE",
    help="Set a constant value when this flag is used."
)

args = parser.parse_args()
print(args)
```

### Output and Explanation

```bash
$ python script.py
Namespace(set_flag='FLAG_INACTIVE')
```

+ Since `--set-flag` wasn't used, the argument falls back to `default="FLAG_INACTIVE"`

```bash
$ python script.py --set-flag
Namespace(set_flag='FLAG_ACTIVE')
```

+ `--set-flag` was included, therefore the parser ignores any user input and always stores the `const="FILE_ACTIVE"` value.

---

## 3. `store_true`

* **Description**: A special case of `store_const`. Stores `True` when the flag is used, `False` otherwise.
* **Usage**: Common for simple boolean flags (e.g., toggles).

### Example

```python
import argparse

parser = argparse.ArgumentParser()

# Argument with store_true
parser.add_argument(
    "--debug",
    action="store_true",
    help="Enable debug mode if this flag is provided."
)

args = parser.parse_args()
print(args)
```

### Output and Explanation

```bash
$ python script.py
Namespace(debug=False)
```

+ Since `--debug` was not used, the default is `False`.

```bash
$ python script.py --debug
Namespace(debug=True)
```

+ The presence of `--debug` sets the value to `True`.

---

## 4. `store_false`

* **Description**: Another special case of `store_const`. Stores `False` when the flag is used, `True` otherwise.
* **Usage**: Used when the presence of the flag disables a behavior.

### Example

```python
import argparse

parser = argparse.ArgumentParser()

# Argument with store_false
parser.add_argument(
    "--no-cache",
    action="store_false",
    help="Disable caching if this flag is provided."
)

args = parser.parse_args()
print(args)
```

### Output and Explanation

```bash
$ python script.py
Namespace(no_cache=True)
```

+ Since `--no-cache` was not provided, the default remains `True`.

```bash
$ python script.py --no-cache
Namespace(no_cache=False)
```

+ The presence of `--no-cache` sets the value to `False`.

---

## 5. `append`

* **Description**: Each time the argument appears on the command line, its value is appended to a list. If the flag is not provided at all, the value defaults to `None`.
* **Usage**: Ideal for specifying multiple inputs under the same flag.

### Example

```python
import argparse

parser = argparse.ArgumentParser()

# Argument with append
parser.add_argument(
    "--tag",
    action="append",
    help="Add one or more tags. Can be used multiple times."
)

args = parser.parse_args()
print(args)
```

### Output and Explanation

```bash
$ python script.py
Namespace(tag=None)
```

+ Since `--tag` was not given, no list is created; value is `None`.

```bash
$ python script.py --tag urgent
Namespace(tag=['urgent'])
```

+ One value is appended, resulting in a list with a single element.

```bash
$ python script.py --tag urgent --tag work --tag personal
Namespace(tag=['urgent', 'work', 'personal'])
```

+ Each occurrence of `--tag` appends its value to the list.

---

## 6. `append_const`

* **Description**: Each occurrence of the argument appends a predefined constant (`const`) to a list (`dest`). The `dest` parameter must be provided to indicate the common destination variable that will hold the list.
* **Usage**: Similar to `append`, but all entries are the same constant.

### Example

```python
import argparse

parser = argparse.ArgumentParser()

# We must also supply const values to use with append_const
parser.add_argument(
    "--red",
    action="append_const",
    const="RED",
    dest="colors",
    help="Append 'RED' to the colors list."
)

parser.add_argument(
    "--blue",
    action="append_const",
    const="BLUE",
    dest="colors",
    help="Append 'BLUE' to the colors list."
)

args = parser.parse_args()
print(args)
```

### Output and Explanation

```bash
$ python script.py
Namespace(colors=None)
```

+ Since no arguments were provided, `colors` is `None`.

```bash
$ python script.py --red
Namespace(colors=['RED'])
```

+ The flag `--red` appends its constant `"RED"` to the `colors` list.

```bash
$ python script.py --red --blue
Namespace(colors=['RED', 'BLUE'])
```

+ Each flag appends its own constant value to the same list.

```bash
$ python script.py --red --red --blue
Namespace(colors=['RED', 'RED', 'BLUE'])
```

+ Repeated usage keeps appending constants, resulting in duplicates.

---

## 7. `count`

* **Description**: Counts the number of times a flag appears, storing an integer. If the flag does not appear, its value defaults to `None` unless the `default` parameter was set.
* **Usage**: Often used for increasing verbosity levels (e.g., `-v`, `-vv`, etc.).

### Example

```python
import argparse

parser = argparse.ArgumentParser()

parser.add_argument(
    "-v", "--verbose",
    action="count",
    default=0,
    help="Increase verbosity level with repeated flags."
)

args = parser.parse_args()
print(args)
```

### Output and Explanation

```bash
$ python script.py
Namespace(verbose=0)
```

+ Since no `-v` or `--verbose` flag was given, `verbose` stays at its default value `0`.

```bash
$ python script.py -v
Namespace(verbose=1)
```

+ A single `-v` increases `verbose` to `1`.

```bash
$ python script.py -vv
Namespace(verbose=2)
```

+ The two `v`s are parsed as two flags. `verbose` increments to `2`.

```bash
$ python script.py --verbose --verbose
Namespace(verbose=2)
```

+ Each `--verbose` increments the counter by one, same as the short form.

---

## 8. `version`

* **Description**: Prints the program’s version (specified via `version=`) and then exits.
* **Usage**: Convenient for exposing version information quickly (`--version` flags).

### Example

```python
import argparse

parser = argparse.ArgumentParser()

parser.add_argument(
    "--version",
    action="version",
    version="MyScript 1.0"
)

args = parser.parse_args()
print(args)
```

### Output and Explanation

```bash
$ python script.py
Namespace()
```

+ Since `--version` was not requested, the program runs normally, printing the empty `Namespace()` because no arguments were parsed.

```bash
$ python script.py --version
MyScript 1.0
```

+ The version string is printed, and the program exits. Notice that `Namespace()` is not shown here, because the program terminated before reaching the `print(args)` line.

---
