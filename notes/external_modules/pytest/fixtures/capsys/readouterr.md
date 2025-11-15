# `capsys.readouterr`

## Overview

The `capsys.readouterr` method is part of the `CaptureFixture` provided by pytest’s `capsys` fixture. Its primary purpose is to **read** and **clear** the text captured so far from standard output (`stdout`) and standard error (`stderr`) during the execution of a test. This allows test code to assert on what has been printed, without interfering with pytest’s internal capturing mechanism.

Typical use cases include:

- Verifying that a function prints expected messages to `stdout` or `stderr`.
- Incrementally checking output at multiple points in a test by calling `readouterr` more than once.
- Clearing the capture buffer between phases of a test.

`readouterr` interacts with pytest’s capturing system: when a test is run with `capsys` enabled, pytest intercepts writes to `sys.stdout` and `sys.stderr` (by default, via file-descriptor level capture). `readouterr` provides a snapshot of what has been captured so far, and then resets its internal buffer so that subsequent output can be captured cleanly.

---

## Syntax

```python
capsys.readouterr()
```

There are no parameters to `readouterr`.

---

## Return Value

* **Type:** A **named tuple**, typically called `CaptureResult`, with two string attributes: `out` and `err`.
* **Description:**

  * `out`: contains the text captured from standard output (`stdout`) so far.
  * `err`: contains the text captured from standard error (`stderr`) so far.
* **Special Notes:**

  * After calling `readouterr()`, the capture buffer is **reset** (cleared), so subsequent output will be captured into a fresh buffer.
  * Capturing continues after the call; `readouterr()` simply takes a snapshot.

---

## Errors and Exceptions

`capsys.readouterr` does **not** typically raise standard Python exceptions during normal usage. There are no documented parameters, so misuse in terms of incorrect input is not possible. However, there are some corner cases and misuse patterns to be aware of:

* **Scope Mismatch / Fixture Misuse:**

  * If you attempt to use `capsys` in a fixture with a broader scope than function (e.g., class- or module-scoped), pytest may raise a `ScopeMismatch` error.
  * Example: Declaring a class-scoped fixture that depends on `capsys` will lead to an error such as:

    ```text
    ScopeMismatch: You tried to access the 'function' scoped fixture 'capsys' with a 'class' scoped request object
    ```

---

## Example

```python
import sys
import pytest

def greet_and_warn():
    print("Hello, user!")
    sys.stderr.write("Warning: something might be wrong\n")

def test_greet_and_warn(capsys):
    # Run the function that writes to stdout and stderr
    greet_and_warn()

    # Capture what has been printed so far
    captured = capsys.readouterr()
    assert captured.out == "Hello, user!\n"
    assert captured.err == "Warning: something might be wrong\n"

    # Now print more to stdout
    print("Goodbye")

    # Capture again: earlier output has been cleared
    later = capsys.readouterr()
    assert later.out == "Goodbye\n"
    assert later.err == ""
```

Another example (combined with debugging):

```python
def test_debug_output(capsys):
    print("Starting test")
    # Maybe some logic here
    print("Mid test")
    captured = capsys.readouterr()
    # During failure, re-emit captured output for debugging
    sys.stdout.write(captured.out)
    sys.stderr.write(captured.err)
    assert "Mid" in captured.out
```

---

## Best Practices

* **Use `readouterr` to isolate phases**: If your test has several logical stages with output in each, call `readouterr()` between stages to snapshot and clear, so you don't mix up output from different parts.
* **Avoid printing too much**: While `capsys` allows you to capture arbitrary output, over-relying on printed text for logic in tests can make them brittle. Where possible, refactor code to return values rather than print, so that you can assert on returned values rather than captured output.
* **Be conscious of capturing mode**: The default capturing mechanism uses file-descriptor level capture. If your code writes to low-level OS file descriptors or uses subprocesses, you may need `capfd` instead.
* **Debug selectively**: If you need to see some output for debugging, use the `capsys.disabled()` context manager rather than globally disabling capture.
* **Version awareness**: The behavior of `readouterr` (named tuple return, clearing buffer) is consistent across modern pytest versions. But always check documentation if you work with very old pytest versions.

---

## Common Pitfalls

* **Assuming `readouterr` does not clear**: A frequent mistake is to assume that `readouterr()` merely reads but does not clear. In fact, it resets the internal buffer, so repeated calls only reflect output since the last snapshot.
* **Losing captured output on failures**: If you call `readouterr` before an assertion and don’t reprint (or otherwise handle) its contents, you might not see that output in the pytest failure report. Because `readouterr` *consumes* the buffer, the captured output may not appear in the test failure summary by default.
* **Fixture scope mismatch**: Trying to use `capsys` in a fixture that has a broader scope than a test function leads to scope errors.
* **Using `capsys` when you need OS-level capture**: If you are testing code that writes to file descriptors (e.g., via `os.write` or subprocesses), `capsys` may not capture that output; you may need `capfd` instead.

---
