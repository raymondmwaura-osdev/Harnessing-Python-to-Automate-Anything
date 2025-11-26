# `subprocess`

## What is the `subprocess` module and why use it

* A *process* is the operating system’s abstraction for a running program. Programs you launch (editors, shells, services) each run as a process.
* The `subprocess` module allows a Python program to **spawn new processes**, **connect to their input/output/error streams**, and **inspect their exit status (return code)**.
* It was introduced to replace older, less flexible mechanisms (e.g., `os.system`, `os.spawn*`, `os.popen`, `popen2`, the old `commands` module) for invoking external programs.
* Use cases include: running shell commands, launching other scripts or executables, automating system tasks (backups, deployment), integrating command-line tools, and capturing their output for further processing.

---

## Primary Interfaces: `run()` vs `Popen()`

The module offers two main modes of usage:

### `subprocess.run()`

* This is the simplest and most common entry point. When you know the command you want to run, don’t need complex interaction, and only care about its final output or return code, `run()` is ideal.
* Typical signature:

  ```python
  subprocess.run(
      args,
      *,
      stdin=None,
      input=None,
      stdout=None,
      stderr=None,
      capture_output=False,
      shell=False,
      cwd=None,
      timeout=None,
      check=False,
      text=None,
      env=None,
      …other options…
  )
  ```

* Returns a `CompletedProcess` object. This object includes:

  * `args`: the command and arguments run.
  * `returncode`: the exit status (0 typically means success).
  * If you requested output capture, `stdout` and/or `stderr` will contain the output/error (either as bytes or decoded text).
* Common useful parameters:

  * `capture_output=True` (captures both `stdout` and `stderr`)
  * `text=True` (decode output to string rather than bytes)
  * `check=True` (raise an exception if the command exits with non-zero status)
  * `shell=True` (run the command through the shell); but this has security implications, especially if inputs are untrusted.

**Example**:

```python
import subprocess

result = subprocess.run(['ls', '-la'], capture_output=True, text=True)
print(result.stdout)
```

**Example** (running a Python script):

```python
import subprocess, sys

result = subprocess.run(
    [sys.executable, 'other_script.py'],
    capture_output=True,
    text=True,
    check=True
)
print(result.stdout)
```

---

### `subprocess.Popen()`

* When you need more control: real-time input/output streaming, interactive commands, background processes, or piping between processes; `Popen` is the tool.
* Typical signature:

  ```python
  subprocess.Popen(
      args,
      stdin=None,
      stdout=None,
      stderr=None,
      shell=False,
      cwd=None,
      env=None,
      text=False,
      …
  )
  ```

* `Popen()` returns a “process handle”; you can then interact with the process (send data, read output), wait for completion, or chain it with other processes.
* Use cases:

  * Send data to process via `stdin=PIPE`, read output from `stdout=PIPE`.
  * Chain multiple subprocesses: e.g., output of one process becomes input to another (like shell pipelines) by passing `stdout=PIPE` from first, `stdin=...` to second.
  * Run long-lived or background processes without blocking the parent Python program.

**Example: sending input and capturing output**

```python
import subprocess

proc = subprocess.Popen(
    ['grep', 'pattern'],
    stdin=subprocess.PIPE, stdout=subprocess.PIPE, stderr=subprocess.PIPE,
    text=True
)
stdout, stderr = proc.communicate("some input text\nanother line\n")
print(stdout)
```

(This runs `grep`, sends input via `stdin`, then captures output.)

---

## Errors, Exceptions, and Best Practices

* **Avoid `shell=True` when possible**, or carefully sanitize inputs if you must use it; shell injection is a real risk.
* **Always handle return codes and exceptions**. With `run()`, use `check=True` if you want a non-zero exit status to raise an exception (`CalledProcessError`), rather than silently continue.
* **Be explicit about capturing output**. If you need output, set `capture_output=True` or explicitly specify `stdout`/`stderr`. Otherwise, output goes directly to the console.
* **Prefer list-style argument passing** instead of a single string when not using shell; this improves portability and reduces parsing ambiguities.
* **For pipelines or multiple commands**, use `Popen` with `PIPE`, rather than chaining shell commands; gives better control and safety.
* **Be aware of cross-platform differences**. Shell commands and behaviors may differ between Unix (Linux/Mac) and Windows. If you need portability, consider using native Python APIs when possible.

---

## Basic Example

```python
import subprocess

try:
    result = subprocess.run(
        ['git', 'status'],
        capture_output=True,
        text=True,
        check=True
    )
except subprocess.CalledProcessError as e:
    print(f"Command failed with exit code {e.returncode}")
    print("Error output:", e.stderr)
else:
    print("Command succeeded. Output:")
    print(result.stdout)
```

If you instead needed to run a long-lasting process and feed it input:

```python
import subprocess

proc = subprocess.Popen(
    ['grep', 'TODO'],
    stdin=subprocess.PIPE,
    stdout=subprocess.PIPE,
    stderr=subprocess.PIPE,
    text=True
)

input_text = "Line1\nTODO: finish docs\nLine3\n"
out, err = proc.communicate(input_text)
print("Matched lines:", out)
```

This demonstrates how Python can orchestrate external tools, capture their outputs, and integrate command-line workflows into scripts or applications.

---
