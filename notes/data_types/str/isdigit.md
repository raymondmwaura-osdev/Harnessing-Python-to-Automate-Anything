# `str.isdigit`

## Overview

The method `str.isdigit` in Python tests whether *all* characters in a string are digits. Its main purpose is to allow validation of strings that are expected to represent non-negative integer values, or more generally to check that a string contains only digit characters. It is frequently used when processing user input, validating ID numbers, phone numbers (after removing formatting), or any textual data that should strictly consist of digits.

---

## Syntax

```
str.isdigit()
```

No parameters are accepted.

---

## Return Value

* **Type:** `bool`
* **Description:** Returns `True` if the string is **not empty** and **every character** in the string is a digit (according to Unicode digit definitions). Returns `False` otherwise.
* **Special Notes:**

  * Unicode digits beyond ASCII (e.g. digits in other scripts, certain superscript digits) are considered valid digits.
  * Strings containing decimal points (`.`), minus sign (`-`), plus sign (`+`), whitespace, alphabetic characters, or any non-digit character cause `isdigit()` to return `False`.
  * An empty string returns `False`.

---

## Example

```python
# Example 1: simple digit-only string
s1 = "123456"
print(s1.isdigit())  # True; all characters are digits

# Example 2: string containing non-digit characters
s2 = "123abc"
print(s2.isdigit())  # False; alphabetic characters present

# Example 3: string with decimal point or sign
s3 = "12.34"
print(s3.isdigit())  # False; '.' is not a digit

s4 = "-123"
print(s4.isdigit())  # False; '-' is not a digit

# Example 4: string with Unicode digits (outside ASCII)
s5 = "\u0967\u0968\u0969"  # Devanagari digits 1,2,3
print(s5.isdigit())  # True; Unicode digits are accepted

# Example 5: empty string
s6 = ""
print(s6.isdigit())  # False; string is empty
```

---
