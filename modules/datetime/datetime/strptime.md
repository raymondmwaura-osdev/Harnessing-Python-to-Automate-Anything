## What Is `datetime.strptime()`?

The `datetime.strptime()` method is part of Python’s built-in `datetime` module. It converts a string representing a date and/or time into a `datetime` object by using a format string composed of special directives.

---

## Syntax Overview

```python
from datetime import datetime

datetime.strptime(date_string, format)
```

* `date_string`: Your input string; like `"21 June, 2018"`
* `format`: A set of codes specifying the shape of that string using directives (e.g., `%d`, `%B`, `%Y`)

---

## Format Directive Cheat Sheet

Here's a quick-reference table of the most common format codes:

| Directive        | Meaning                                                              | Example                        |
| ---------------- | -------------------------------------------------------------------- | ------------------------------ |
| `%Y`             | Four-digit year                                                      | 2025                           |
| `%y`             | Two-digit year (POSIX mapping: 69–99 → 1969–1999; 00–68 → 2000–2068) | 23 (→ 2023)                    |
| `%m`             | Month (01–12)                                                        | 08                             |
| `%B`             | Full month name                                                      | August                         |
| `%d`             | Day of month (01–31)                                                 | 23                             |
| `%H`             | Hour (24-hour clock) (00–23)                                         | 13                             |
| `%I`             | Hour (12-hour clock) (01–12)                                         | 01                             |
| `%p`             | AM or PM designation                                                 | PM                             |
| `%M`             | Minute (00–59)                                                       | 45                             |
| `%S`             | Second (00–59)                                                       | 30                             |
| `%f`             | Microseconds (000000–999999)                                         | 123456                         |
| `%z`             | UTC offset (+HHMM or –HHMM)                                          | +0200                          |
| `%Z`             | Time zone name                                                       | UTC                            |
| `%a`, `%A`       | Weekday abbrev/full name                                             | Tue / Tuesday                  |
| `%j`             | Day of year (001–366)                                                | 123                            |
| `%U`, `%W`       | Week number (Sunday- or Monday-first)                                | 35                             |
| `%c`, `%x`, `%X` | Locale-specific the defaults                                         | Mon Sep 24 07:06:05 2025, etc. |

---

## How It Works, with Examples

### 1. **Basic date parsing**

```python
from datetime import datetime

date_obj = datetime.strptime("21 June, 2018", "%d %B, %Y")
print(date_obj)  # → 2018-06-21 00:00:00
```

This example shows how the string turns into a `datetime` object with midnight as default time.

### 2. **Full datetime with time**

```python
datetime.strptime("12/11/2018 09:15:32", "%d/%m/%Y %H:%M:%S")
# → 2018-11-12 09:15:32

# But if you flip day/month:
datetime.strptime("12/11/2018 09:15:32", "%m/%d/%Y %H:%M:%S")
# → 2018-12-11 09:15:32
```

Warning: Format matters. Swapping `%d` and `%m` flips day and month.

### 3. **Microseconds included**

```python
timestamp = "25/05/99 02:35:5.523"
fmt = "%d/%m/%y %H:%M:%S.%f"
date = datetime.strptime(timestamp, fmt)
print(date.microsecond)  # → 523000
```

See how `%f` handles fractional seconds.

---

## Common Pitfalls & Gotchas

### Mismatched format → `ValueError`

You’ll get:

```
ValueError: time data '12/11/2018' does not match format '%d %m %Y'
```

When what you expect and what you pass don’t align.

### "Unconverted data remains"

A `ValueError` is raised if your string contains extra data not accounted for in the format.

```python
from datetime import datetime

date_str = "2022-01-01 13:30:45 ExtraData"
datetime.strptime(date_str, "%Y-%m-%d %H:%M:%S")
# → ValueError: unconverted data remains:  ExtraData
```

Trim the extra data or expand your format to match.

### Two-digit year ambiguity

Remember POSIX rules:

* `69–99` → 1969–1999
* `00–68` → 2000–2068

### Timezones and `tzinfo`

`strptime()` alone doesn’t assign timezone info; unless you explicitly include `%z` or `%Z`, and even then, true timezone handling needs you to add awareness manually using `tzinfo` or libraries like `zoneinfo`.

---

## Best Practices

* **Always match formats exactly.** Even delimiters like `/`, `-`, or spaces matter.
* **Use four-digit years (`%Y`) when in doubt**; two-digit years can bite you later.
* **Wrap parsing in try-except blocks** for cleaner errors and fallback logic.
* **Prefer ISO standard date formats (`YYYY-MM-DD`)**; they’re unambiguous and widely readable.
* **Include time components even if it's midnight**, so your object is fully formed.

---
