# `subprocess.run`

## Overview

The `subprocess.run` function in Python provides a high-level, convenient interface for launching external commands (subprocesses), waiting for them to complete, and obtaining their results (exit code, standard output, standard error).

Its primary purpose is to allow Python scripts to execute external programs or shell commands in a controlled and configurable manner. Use cases include: automating shell commands, invoking other executables or scripts, capturing their output or errors, managing timeouts, redirecting input/output/error streams, and integrating command-line tools within Python workflows.

`subprocess.run` integrates with other features of Python by returning a structured result (a `CompletedProcess` object) that exposes attributes for output and error data, and can work with text or binary streams depending on configuration, making it suitable for further processing in Python code.

Because `run` is essentially a wrapper around the lower-level `Popen` interface, most advanced process-management parameters allowed by `Popen` are available via `run`, while simplified defaults make it easier for common use cases.

---

## Syntax

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
    encoding=None,
    errors=None,
    text=None,
    env=None,
    universal_newlines=None,
    **kwargs
)
```

*(The parameter list shown corresponds to the most common and frequently used arguments.)*

---

## Parameters

* **`args`**

  * **Type:** `list[str]`, or `str` (if `shell=True`)
  * **Description:** Specifies the command to run and its arguments. If given as a list, each element corresponds to a command or argument token; if given as a string and `shell=True`, the entire string is executed by the shell.
  * **Default Value:** (no default; required)
  * **Constraints:** If `shell=False` (the default), `args` must be a sequence; if `shell=True`, a string may be used.

* **`stdin`**

  * **Type:** file-like object, or `subprocess.PIPE`, or `None`
  * **Description:** Determines how the standard input (stdin) of the subprocess is handled. If set to `PIPE`, the subprocess’s stdin is connected to a pipe so that the parent can send data. If `None`, the subprocess inherits stdin from the parent (typically the console).
  * **Default Value:** `None`
  * **Constraints:** Cannot be used simultaneously with `input`.

* **`input`**

  * **Type:** `bytes` (default binary mode) or `str` (if `text=True`, or encoding/errors specified)
  * **Description:** Data to send to the subprocess via stdin. Internally passed to `Popen.communicate()`. If provided, the internal `Popen` object will be created with `stdin=PIPE`.
  * **Default Value:** `None` (no input sent)
  * **Constraints:** Cannot be used simultaneously with `stdin`.

* **`stdout`**

  * **Type:** file-like object, `subprocess.PIPE`, `subprocess.DEVNULL`, or `None`
  * **Description:** Controls where the subprocess’s standard output is directed. Use `PIPE` to capture output, `DEVNULL` to discard it, a file handle to write to a file, or `None` to inherit the parent’s stdout.
  * **Default Value:** `None` (inherits parent stdout)
  * **Constraints:** Cannot be used simultaneously with `capture_output=True`.

* **`stderr`**

  * **Type:** file-like object, `subprocess.PIPE`, `subprocess.DEVNULL`, `subprocess.STDOUT`, or `None`
  * **Description:** Controls the subprocess’s standard error stream in the same way as `stdout`. Setting `subprocess.STDOUT` merges stderr into stdout.
  * **Default Value:** `None` (inherits parent stderr)
  * **Constraints:** Cannot be used simultaneously with `capture_output=True`.

* **`capture_output`**

  * **Type:** `bool`
  * **Description:** If `True`, both stdout and stderr will be captured (equivalent internally to setting `stdout=PIPE` and `stderr=PIPE`).
  * **Default Value:** `False`
  * **Constraints:** Cannot be used if either `stdout` or `stderr` is explicitly specified.

* **`shell`**

  * **Type:** `bool`
  * **Description:** If `True`, runs the command through the shell (e.g., `/bin/sh` on Unix or `cmd.exe` on Windows), allowing shell features like pipes, wildcard expansion, and environment variable interpolation. If `False`, the command is executed directly without shell.
  * **Default Value:** `False`
  * **Constraints / Risk:** Using `shell=True` introduces security risks (shell injection) if `args` originates from untrusted input.

* **`cwd`**

  * **Type:** `str` or `path-like` or `None`
  * **Description:** If provided, sets the working directory for the subprocess.
  * **Default Value:** `None` (inherit current working directory)

* **`timeout`**

  * **Type:** `float` or `int` (seconds), or `None`
  * **Description:** Maximum number of seconds to wait for the subprocess to complete. Passed to `Popen.communicate()`. If the timeout expires, the subprocess is killed and a `TimeoutExpired` exception is raised.
  * **Default Value:** `None` (wait indefinitely)

* **`check`**

  * **Type:** `bool`
  * **Description:** If `True`, and the subprocess exits with a non-zero return code, a `CalledProcessError` exception is raised. If `False`, no exception is raised, and the caller must inspect the `returncode`.
  * **Default Value:** `False`

* **`encoding`**

  * **Type:** `str`, or `None`
  * **Description:** Specifies the text encoding to use for stdin/stdout/stderr when text mode is requested. If set, streams operate in text mode with given encoding.
  * **Default Value:** `None` (binary mode unless `text=True` or `universal_newlines=True`)

* **`errors`**

  * **Type:** `str`, or `None`
  * **Description:** Specifies how encoding errors are handled (e.g., `'replace'`, `'ignore'`) when using text mode.
  * **Default Value:** `None`

* **`text`**

  * **Type:** `bool`
  * **Description:** If `True`, equivalent to `universal_newlines=True`: subprocess stdin/stdout/stderr are opened in text mode, and I/O uses strings instead of bytes. If `False` (default) streams use bytes.
  * **Default Value:** `False`

* **`universal_newlines`**

  * **Type:** `bool` or `None`
  * **Description:** Legacy alias for `text`. If present and `True`, has same effect as `text=True`.
  * **Default Value:** `None`

* **`env`**

  * **Type:** `dict` (mapping of environment variables), or `None`
  * **Description:** If provided, used as the environment variables for the subprocess (instead of inheriting the parent’s environment).
  * **Default Value:** `None` (inherit parent environment)

* **`kwargs`**

  * **Type:** various (as accepted by `Popen`)
  * **Description:** Additional keyword arguments passed directly to the underlying `Popen` constructor; e.g., `bufsize`, `preexec_fn`, file descriptors, etc. Use these for advanced control beyond the common parameters.
  * **Default Value:** none

---

## Return Value

* **Type:** `subprocess.CompletedProcess`

* **Description:** On successful completion, `run()` returns a `CompletedProcess` instance. This object has at least the following attributes:

  * `args`: the arguments passed to the command (list or string)
  * `returncode`: integer exit status of the subprocess (0 typically means success)
  * `stdout`: captured standard output (if `stdout=PIPE` or `capture_output=True`; type is `bytes` or `str` depending on text mode)
  * `stderr`: captured standard error (similarly) if requested

* **Special Notes:**

  * If neither stdout nor stderr is captured (default), those attributes will be `None`.
  * If `check=True` is provided and the subprocess exits with non-zero return code, instead of returning a `CompletedProcess`, a `subprocess.CalledProcessError` exception is raised.

---

## Errors and Exceptions

Possible exceptions raised by `subprocess.run`:

* **`OSError`** (or subclass, e.g., `FileNotFoundError`)

  * **Cause:** Attempting to execute a non-existent program, or lacking permissions, or invalid executable path.
  * **Example:**

    ```python
    import subprocess
    subprocess.run(['nonexistent_command'])
    # Raises FileNotFoundError (subclass of OSError)
    ```

* **`ValueError`**

  * **Cause:** Invalid combination of arguments passed to underlying `Popen` (e.g., specifying both `capture_output=True` and `stdout=PIPE`).
  * **Example:**

    ```python
    import subprocess
    subprocess.run(['ls'], stdout=subprocess.PIPE, capture_output=True)
    # Raises ValueError
    ```

* **`subprocess.TimeoutExpired`**

  * **Cause:** Subprocess does not complete within the specified `timeout` period. The child process is killed, then this exception is re-raised.
  * **Example:**

    ```python
    import subprocess
    subprocess.run(['sleep', '10'], timeout=1)
    # Raises subprocess.TimeoutExpired after ~1 second
    ```

* **`subprocess.CalledProcessError`**

  * **Cause:** `check=True` was specified and the subprocess exited with non-zero return code. The exception holds attributes including `returncode`, and captured output if any.
  * **Example:**

    ```python
    import subprocess
    subprocess.run(['ls', 'nonexistent_dir'], check=True)
    # Raises CalledProcessError
    ```

* **Other exceptions** may arise from the underlying `Popen` call depending on platform and OS-level constraints. The module defines a base exception type `SubprocessError`, from which these exceptions inherit.

---

## Example

```python
# Example 1; basic command, no output capture
import subprocess

result = subprocess.run(['echo', 'Hello, world!'])
# result.returncode == 0, stdout and stderr are None (inherited from parent)

# Example 2; capture output as text
import subprocess

result = subprocess.run(
    ['echo', 'Hello, External!'],
    capture_output=True,
    text=True
)
print("Output:", result.stdout)       # prints "Hello, External!\n"
print("Return code:", result.returncode)

# Example 3; check for failure; raise on non-zero exit
import subprocess

try:
    subprocess.run(['ls', 'nonexistent_directory'], check=True, capture_output=True, text=True)
except subprocess.CalledProcessError as e:
    print("Error: command failed with code", e.returncode)
    print("stderr:", e.stderr)
```

---

## Best Practices

* Prefer **list-style arguments** (`['cmd', 'arg1', 'arg2']`) over string commands; improves security and avoids shell parsing issues.
* Use `capture_output=True` or explicit `stdout=PIPE`, `stderr=PIPE` when you need subprocess output/error; otherwise let output go to parent to avoid buffering issues.
* Use `text=True` (or `encoding`, `errors`) when you need human-readable strings, else accept bytes.
* Use `check=True` when failure of subprocess should be treated as an exception; otherwise manually inspect `returncode`.
* Avoid `shell=True` unless necessary (e.g., you need shell features). If used, be cautious about security implications, especially with untrusted input.
* When expecting potentially long-running external commands, consider using `timeout` to avoid hangs.
* For advanced use cases (streams piping, interactive subprocess, complex redirection) consider using lower-level `Popen` directly; `run` is optimized for simplicity and synchronous execution.

---

## Common Pitfalls

* Mistakenly supplying both `capture_output=True` and explicit `stdout`/`stderr`, which raises `ValueError`.
* Assuming output is string when default is bytes; forgetting to set `text=True` or decode manually.
* Relying on shell-specific features (pipes, redirects, wildcard expansion) while `shell=False`; commands will fail or behave unexpectedly.
* Ignoring return codes when `check=False`, leading to silent failures.
* Not handling `TimeoutExpired`, causing unexpected crashes or zombies if subprocess overruns.
* Passing invalid paths or commands (leading to `OSError`), or invalid parameter combinations.
* Using `run` in performance-critical loops for many processes; overhead may accumulate; consider reusing processes or using `Popen` for more control.

---
