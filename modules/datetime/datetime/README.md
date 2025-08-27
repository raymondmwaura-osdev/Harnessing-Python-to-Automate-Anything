# `datetime.datetime`

The `datetime.datetime` class (commonly referred to simply as *datetime*) is a component of Python’s standard `datetime` module. It represents a **specific point in time**, combining both a calendar date and a time of day into a single object.

---

## Purpose and Utility

* Merges date and time into a unified representation (year, month, day, hour, minute, second, microsecond).
* Useful for expressing exact moments, often in conjunction with timezone awareness (through its `tzinfo` attribute).
* Designed for both straightforward attribute access and time-based arithmetic, such as comparison, addition, or subtraction with `timedelta`.

---

## Construction

### Syntax

```python
class datetime.datetime(year, month, day,
                       hour=0, minute=0, second=0,
                       microsecond=0, tzinfo=None, *, fold=0)
```

* **Mandatory parameters**: `year`, `month`, `day`.
* **Optional parameters**: `hour`, `minute`, `second`, `microsecond`, `tzinfo`, and `fold`, each defaulting to 0 or `None` as appropriate.
* **Ranges**:

  * `year`: between `MINYEAR` (1) and `MAXYEAR` (9999)
  * `month`, `day`, `hour`, `minute`, `second`, `microsecond`: each within standard calendar/time limits
  * `fold`: indicates ambiguous times during daylight savings transitions (0 or 1).

### Example

```python
from datetime import datetime

# Creating a datetime for October 22, 2022 at 06:02:32.005456
dt_instance = datetime(2022, 10, 22, 6, 2, 32, 5456)
print(dt_instance)
# Output: 2022-10-22 06:02:32.005456
```

Here, date and time components combine to form a precise timestamp.

---

## Typical Use Cases

+ Obtain current local timestamp: Use `datetime.now()` to capture the present moment without timezone info.
+ Access individual components: Retrieve `year`, `month`, `day`, `hour`, etc., via attribute accessors.
+ Time arithmetic: Perform operations like addition or subtraction using `timedelta` objects.

---

## Context Within the `datetime` Module

The `datetime` module includes several closely related date-time classes:

* `date`: Represents only a calendar date (year, month, day)
* `time`: Represents only a time independent of date (hour, minute, second, microsecond, including optional timezone info)
* `timedelta`: Represents a duration or time difference
* `tzinfo` and `timezone`: Used to incorporate timezone data.

The `datetime.datetime` class integrates the capabilities of both `date` and `time`, providing a flexible foundation for precise temporal representation and manipulation.

---
