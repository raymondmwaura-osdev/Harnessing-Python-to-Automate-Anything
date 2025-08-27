#`datetime`

The **`datetime` module** is part of Python’s standard library and provides classes and functions for working with dates and times. It enables programs to represent, manipulate, and format temporal data at different levels of precision; from whole days to fractions of a second.

---

## Purpose and Capabilities

The module is designed to:

* Represent calendar dates, times of day, or full timestamps.
* Perform arithmetic with dates and times (e.g., differences, offsets, durations).
* Handle conversions between different representations, such as formatted strings and structured objects.
* Support time zones and daylight saving transitions through its `tzinfo` framework.

---

## Core Classes

The module consists of several closely related classes:

* **`date`**: Represents a calendar date (year, month, day).
* **`time`**: Represents a time of day, independent of any date (hour, minute, second, microsecond, with optional time zone).
* **`datetime`**: Combines `date` and `time` into a single object, representing an exact point in time.
* **`timedelta`**: Represents the difference between two dates or times (a duration).
* **`tzinfo`**: Abstract base class for time zone information.
* **`timezone`**: A concrete implementation of `tzinfo` for fixed UTC offsets.

---

## Common Use Cases

* Retrieving the **current date and time** (`datetime.now()`).
* **Formatting and parsing** strings into date-time objects (`strftime`, `strptime`).
* **Computing differences** between two dates (yielding a `timedelta`).
* **Shifting** a date or time forward or backward by a defined duration.
* Working with **time zones** for global or localized applications.

---
