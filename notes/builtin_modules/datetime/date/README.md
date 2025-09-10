# `datetime.date`

## Introduction

The `datetime.date` class provides a robust and efficient representation of a calendar date (specifically, a year, month, and day) without any associated time-of-day information. Useful when time-of-day details (hours, minutes, seconds) are irrelevant or unwarranted. It excludes complexity such as time zones or timestamps; keeping it clean, minimalistic, and ideal for tasks like logging birthdays, deadlines, or academic anniversaries.

---

## Core Characteristics

1. **Constructor Signature**
   You instantiate a date via:

   ```python
   datetime.date(year, month, day)
   ```

   * All three parameters are mandatory integers.
   * Acceptable ranges:

     * `year`: between `MINYEAR = 1` and `MAXYEAR = 9999`
     * `month`: `1` to `12`
     * `day`: valid day number for the given month and year.

   Invalid types raise `TypeError`; out-of-range integers trigger `ValueError`.

2. **Attributes**

   * `.year`, `.month`, `.day` properties give direct access to their respective integer values.

3. **Range and Resolution**

   * Date objects can span from **January 1, 0001** to **December 31, 9999**; a vast temporal canvas.
   * `.min` and `.max` class attributes reflect these bounds.

4. **Utility Methods**

   * `today()`: returns current local date as a `date` object.
   * `isoformat()`: formats the date as an ISO-8601 string (`YYYY-MM-DD`).
   * Other helpful methods include `weekday()`, `isoweekday()`, `ctime()`, and conversions like `fromisoformat()` or `fromordinal()`.

---

## **`date` vs. `datetime`**

* **`date`**: Holds only year, month, and day.
* **`datetime`**: Inherits from `date` and adds time components; hour, minute, second, microsecond, and optional timezone (`tzinfo`).

This means:

* `isinstance(datetime.now(), date)` returns `True`; `datetime` is indeed a subclass of `date`.
* If you need to distinguish them precisely, use `type(x) is datetime.date` rather than `isinstance`.

---
