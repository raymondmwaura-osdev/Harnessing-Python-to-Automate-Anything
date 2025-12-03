# `str.isupper`

## Overview

The method `str.isupper` in Python is used to test whether a string’s *cased characters* are all uppercase. Its primary purpose is to provide a quick way to check if a string is in uppercase form (or at least has all its alphabetic / cased characters uppercase). It is commonly used in input validation, data-cleaning, normalization tasks, or anywhere the case (uppercase vs lowercase) of letters matters.

This method considers only *cased characters* (i.e. characters that have a notion of “upper” vs “lower” case, such as alphabetic letters). Non-cased characters (digits, punctuation, whitespace, symbols) are ignored by the case test. However, for the method to return `True`, there must be **at least one** cased character in the string, and **all** such cased characters must be uppercase. If the string is empty or contains no cased characters, the method returns `False`.

---

## Syntax

```
str.isupper()
```

No parameters are accepted.

---

## Return Value

* **Type:** `bool`
* **Description:** Returns `True` if **all** cased characters in the string are uppercase **and** there is at least one cased character. Returns `False` otherwise.
* **Special Notes:** If the string is empty, or contains no cased characters (e.g. only digits, punctuation, whitespace, or symbols), it returns `False`.

---

## Example

```python
# Example 1: simple uppercase string
s1 = "PYTHON"
print(s1.isupper())  # True, all cased characters are uppercase

# Example 2: mixed-case string
s2 = "Python"
print(s2.isupper())  # False, contains lowercase 'ython'

# Example 3: string with non-alphabetic characters + uppercase letters
s3 = "HELLO 123!"
print(s3.isupper())  # True, the cased characters 'HELLO' are uppercase; digits and punctuation are ignored

# Example 4: string with no cased characters
s4 = "12345!"
print(s4.isupper())  # False, there is no cased character
```

---
