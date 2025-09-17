# Attributes

A `timedelta` object in Python represents a duration, which is the difference between two `datetime` or `date` objects, accurate to the microsecond. Internally, every `timedelta` instance is represented only in terms of **days**, **seconds**, and **microseconds**. Although additional keyword arguments such as weeks, hours, minutes, or milliseconds can be provided during construction, these are converted into the canonical three attributes through a process of normalization.

---

## The Three Attributes

### 1. `days`

The `days` attribute is an integer that may be either positive or negative. Its range extends from `-999,999,999` to `+999,999,999` inclusive. It represents the number of whole days encompassed by the duration. Any input arguments such as weeks or large numbers of hours and minutes are aggregated into this attribute. In addition, excess time components from the `seconds` argument (for example, more than 24 hours) are carried over and expressed as additional days. For negative durations, the `days` attribute is adjusted accordingly and may carry the sign of the interval.

### 2. `seconds`

The `seconds` attribute is always an integer in the range from `0` up to `86,399`. It represents the number of seconds remaining after the day portion has been accounted for. In other words, once the whole number of days is extracted into the `days` attribute, the leftover number of seconds within the day is stored in `seconds`. If more than 86,400 seconds are provided during initialization, the surplus is added to `days`. Similarly, if negative seconds are provided, normalization decreases the `days` value and adjusts `seconds` into the proper range.

### 3. `microseconds`

The `microseconds` attribute is an integer ranging from `0` up to `999,999`. It stores the remainder of the duration smaller than one second. Inputs such as milliseconds or fractional seconds are converted into microseconds. If more than one million microseconds are supplied, they are carried into the `seconds` attribute. If negative microseconds are given, normalization reduces the `days` and `seconds` components while adjusting `microseconds` into its defined range. When floats are supplied, fractional parts are rounded to the nearest microsecond using the "round half to even" rule.

---

## Normalization Behavior

Normalization guarantees that each `timedelta` object has a unique and consistent internal state. All inputs are first converted into days, seconds, and microseconds, after which surplus values are carried upward into higher units. For example, supplying `timedelta(hours=25)` results in one extra day being added to `days` and the remainder of one hour stored in `seconds`. A negative input such as `timedelta(microseconds=-1)` is normalized into `days = -1`, `seconds = 86399`, and `microseconds = 999999`, which together represent one microsecond less than a full day.

---

## Example Illustrations

```python
from datetime import timedelta

# Example 1: Overflowing components
delta1 = timedelta(days=2, hours=25, minutes=61, seconds=3661, microseconds=1)
print(delta1.days, delta1.seconds, delta1.microseconds)
# Normalized into 3 days plus leftover seconds and one microsecond.

# Example 2: Negative values
delta2 = timedelta(microseconds=-1)
print(delta2.days, delta2.seconds, delta2.microseconds)
# Normalized into (-1, 86399, 999999).
```

---

## Implications and Use Cases

The three attributes must always be considered together. The `days` attribute alone does not capture smaller components of the duration, while `seconds` and `microseconds` are limited to sub-day and sub-second values. For this reason, when one requires a total measure, the `total_seconds()` method should be preferred. This method converts the entire normalized duration into a floating-point value expressed in seconds.

---

## Best Practices

1. Prefer `total_seconds()` when comparing or performing arithmetic with durations, as it accounts for all three attributes simultaneously.
2. Be cautious when handling negative durations, because the attributes are normalized in ways that can appear counterintuitive.
3. Avoid assuming that `.seconds` represents the entire duration in seconds; it only expresses the leftover portion after full days are removed.
4. Ensure clarity when serializing or formatting durations, as human-readable forms may obscure the precise breakdown of days, seconds, and microseconds.

---

## Common Pitfalls

1. Misinterpreting `.seconds` as the total number of seconds in the interval rather than the remainder within the day.
2. Forgetting that `.microseconds` is always non-negative and less than one million, even when negative values were provided at construction.
3. Experiencing subtle rounding issues when passing floating-point arguments that convert into fractional microseconds.
4. Encountering confusing string representations when dealing with negative timedeltas, as they are normalized into forms like `-1 day, 23:00:00`.
5. Hitting `OverflowError` if the resulting `days` value after normalization exceeds the permitted range.

---
