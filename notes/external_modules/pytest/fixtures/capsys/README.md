# `capsys`

## Purpose of `capsys`

* `capsys` is a built-in pytest fixture designed to **capture** output that your code writes to the *standard output* (`stdout`) and *standard error* (`stderr`) streams.
* This fixture is particularly useful in tests where you need to **verify** what is printed (e.g., via `print` or `sys.stderr.write`) without polluting the test runner’s console.
* After the test function ends, pytest restores the original `sys.stdout` and `sys.stderr`.

---

## How `capsys` Works Under the Hood

* Internally, pytest uses a capture manager (`CaptureManager`) that intercepts writes to Python’s `sys.stdout`/`sys.stderr` when capturing is enabled.
* The default capturing mechanism is **file descriptor (FD) level**, meaning it intercepts writes at the OS-level file descriptors (1 for stdout, 2 for stderr).
* Alternative capturing strategies (e.g., `sys`-level or “tee”) can be selected via pytest command-line options (e.g., `--capture=sys` or `--capture=tee-sys`).

---

## API of `capsys`

When you declare `capsys` as a parameter in a test function, pytest injects a **CaptureFixture** object. Key methods and behaviors:

1. **`capsys.readouterr()`**

   * This method returns a snapshot of the captured output so far.
   * It returns a *named tuple* (often called `CaptureResult`) with two attributes:
     * `out`: the captured standard output (`str`)
     * `err`: the captured standard error (`str`)
   * Invoking `readouterr()` **clears** the internal buffer, so subsequent calls only return new output generated after the previous snapshot.

2. **`capsys.disabled()` (context manager)**

   * Using `with capsys.disabled():` temporarily *disables* capturing inside the `with` block.
   * This is useful when you want certain output to actually go to the console (e.g., for debugging) and not be swallowed by pytest’s capture mechanism.

---

## Basic Usage Example

Here is a minimal illustrative example:

```python
def function_under_test():
    print("Hello, stdout")
    import sys
    sys.stderr.write("Hello, stderr\n")

def test_function_output(capsys):
    function_under_test()
    captured = capsys.readouterr()
    assert captured.out == "Hello, stdout\n"
    assert captured.err == "Hello, stderr\n"

    # If more output is generated later:
    print("Another line")
    second = capsys.readouterr()
    assert second.out == "Another line\n"
    assert second.err == ""
```

Explanation:

* The first call to `readouterr()` captures what `function_under_test` printed.
* After that, capturing continues; a second call to `readouterr()` captures subsequent output.

---

## Advanced Patterns and Caveats

1. **Mixing with `capfd` / `capsysbinary`**

   * `capfd` is similar to `capsys`, but captures at the *file descriptor* level, which is useful for capturing output from C extensions or subprocesses that write directly to OS file descriptors.
   * `capsysbinary` captures `stdout` and `stderr` as **bytes** rather than strings (i.e., `out` and `err` will be `bytes`).
   * Important: You cannot request more than one of these capture fixtures in a single test (e.g., you should not have both `capsys` and `capfd` in the same test), because pytest enforces only one capture fixture per test.

2. **Performance Concerns**

   * Capturing incurs a slight overhead. For output-intensive tests, consider whether you need to assert on every printed string; sometimes it's more efficient to refactor the code so that the logic returns values rather than printing directly.

3. **Debugging Output**

   * If you want some output to be visible when running pytest (e.g., for debugging), you can disable capturing using either `-s` when running pytest (which disables capturing altogether) or using `capsys.disabled()` within a test.
   * Be cautious: disabling capturing globally (via `-s`) can make test output noisy and harder to parse; use `disabled()` context where more selective control is required.

4. **Scope and Fixture Limitations**

   * The `capsys` fixture is per-test (function) scoped by default: it starts capturing when the test begins and restores streams when the test ends.
   * Because of how pytest’s capture manager works, using `capsys` *inside other fixtures* (especially with unusual fixture scopes) can lead to complications. For instance, you should avoid relying on captured output across multiple test phases unless carefully orchestrated.

---

## Practical Advice and Best Practices

* Use `capsys` when your code writes to `print` or `sys.stderr` and you want deterministic testing of that output.
* Prefer `capsys.readouterr()` over manually redirecting `sys.stdout` in tests: pytest’s built-in fixture is more robust and integrates well with its test lifecycle.
* If you only care about *some* lines of output (not everything), you can call `readouterr()` multiple times, or buffer only the relevant part.
* When debugging, leverage the `with capsys.disabled()` context to allow selective passthrough of output.
* Avoid overusing print-based verification: where possible, design functions to *return* values rather than *print* them, which makes testing simpler and more efficient.

---
