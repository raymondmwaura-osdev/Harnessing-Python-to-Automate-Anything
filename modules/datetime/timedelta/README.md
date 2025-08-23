# `timedelta`

## Introduction

The `timedelta` class, available in Python’s `datetime` module, provides a representation of a **duration**; the difference between two points in time. Unlike `date` or `datetime` objects, which mark fixed points on the calendar, a `timedelta` object describes a span of time with microsecond precision. It is an immutable data type, meaning that once created, its value cannot be altered.

---

## Purpose and Use

The primary role of `timedelta` is to allow precise handling of time intervals. It is often employed in operations such as:

* Calculating the difference between two `datetime` or `date` objects.
* Adding or subtracting durations from existing `datetime` objects.
* Representing time spans for scheduling, expiration, or measurement tasks.

---

## Parameters

A `timedelta` object can be constructed using the following keyword arguments, all of which default to zero:

* `days`
* `seconds`
* `microseconds`
* `milliseconds`
* `minutes`
* `hours`
* `weeks`

Although these parameters may be specified independently, internally the class normalizes values to **days, seconds, and microseconds**. For example:

* 1 millisecond = 1,000 microseconds
* 1 minute = 60 seconds
* 1 hour = 3,600 seconds
* 1 week = 7 days

---

## Example

```python
from datetime import timedelta

# A duration of 10 days
delta = timedelta(days=10)
print(delta)
```

**Output:**

```
10 days, 0:00:00
```

This object represents a span of exactly ten days. By default, hours, minutes, and smaller units are set to zero.

---

## Example: Combining Parameters

```python
from datetime import timedelta

# A duration of 2 weeks, 3 days, 4 hours, and 30 minutes
delta = timedelta(weeks=2, days=3, hours=4, minutes=30)
print(delta)
```

**Output:**

```
17 days, 4:30:00
```

In this case:

* `2 weeks` is normalized into 14 days.
* Added `3 days` results in 17 days.
* `4 hours` and `30 minutes` are converted into seconds.

The final result reflects the normalized form: 17 days, 4 hours, and 30 minutes.

---

## Arithmetic with `timedelta`

`timedelta` objects are particularly useful in arithmetic operations:

```python
from datetime import datetime, timedelta

# Current datetime
now = datetime.now()

# Add 5 days
future_date = now + timedelta(days=5)

# Subtract 3 hours
past_date = now - timedelta(hours=3)

print("Now:       ", now)
print("Future:    ", future_date)
print("Past:      ", past_date)
```

This illustrates how durations can shift points in time forward or backward.

---
