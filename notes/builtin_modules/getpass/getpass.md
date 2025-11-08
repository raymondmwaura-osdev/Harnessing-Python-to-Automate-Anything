# `getpass.getpass`

## Overview

The function `getpass.getpass` from Python’s standard library module `getpass` prompts the user for a password (or other secret input) **without echoing** the input back to the screen (or optionally showing a masking character). Its primary purpose is to secure terminal-based password input so that someone looking over the shoulder or at screen-recordings cannot see the password being typed. Use cases include command-line authentication prompts, interactive scripts requiring secret keys, or any scenario where `input()` would expose the characters typed. It behaves like `input()` except that the characters typed are not displayed (or displayed masked), and internally it interacts with terminal echo control (via OS APIs) to disable display of typed characters.

---

## Syntax

```python
getpass.getpass(prompt='Password: ', stream=None, *, echo_char=None)
```

---

## Parameters

* **prompt**

  * **Type:** `str`
  * **Description:** String displayed to the user before reading their input. Defaults to `'Password: '`.
  * **Default Value:** `'Password: '`
  * **Constraints:** Should be a string. If omitted, default is used.

* **stream**

  * **Type:** file-like object (with `.write()` method) or `None`
  * **Description:** On Unix, this is the file-like object to which the prompt is written. Defaults to the controlling terminal (e.g., `/dev/tty`) or `sys.stderr`. On Windows this parameter is ignored (the prompt defaults to standard error).
  * **Default Value:** `None`
  * **Constraints:** If provided, must be a writable stream; using `stream` may affect where the prompt appears.

* **echo_char** (available from Python 3.14 onward)

  * **Type:** `str` (single printable ASCII character) or `None`
  * **Description:** Controls how input is displayed while typing: if `None`, no characters are displayed (default). If set to a single character (e.g., `'*'`), each typed character is replaced by that character on the screen.
  * **Default Value:** `None`
  * **Constraints:** Only available in Python 3.14+; if non-`None`, must be exactly one printable ASCII character. On Unix, enabling echo_char uses non-canonical terminal mode, which may alter key-handling (e.g., line editing shortcuts may not work).

---

## Return Value

* **Type:** `str`
* **Description:** Returns the string entered by the user (excluding the trailing newline). Input is read until the user presses “Enter”.
* **Special Notes:**

  * If echo-free input cannot be achieved (for example when running in certain IDE consoles or non-tty contexts), `getpass` may fall back to reading via `sys.stdin` with echo enabled and issue a `GetPassWarning`.
  * The returned string may be empty if the user simply presses “Enter” without typing anything.

---

## Errors and Exceptions

* **`getpass.GetPassWarning`** (a subclass of `UserWarning`)

  * **Cause:** Occurs when the module is unable to disable input echo (for example in non-TTY environments or certain IDE consoles) and thus the characters may be visible.
  * **Example:** Running `getpass.getpass()` inside an IDE terminal may produce this warning and echo the password anyway.

* **`OSError`**

  * **Cause:** On Unix, if no controlling terminal is available (and fallback fails) the module may raise an `OSError`. For example, when redirecting input from a pipe in a script.
  * **Example:**

    ```bash
    echo "mypwd" | python3 myscript.py
    ```

    Could trigger `OSError: (25, 'Inappropriate ioctl for device')` when getpass tries to use a terminal‐specific ioctl.

* **`TypeError`**

  * **Cause:** If parameters of incorrect type are passed (e.g., `echo_char` not a single character string) or module mis-used.
  * **Example:**

    ```python
    import getpass
    # incorrect: echo_char as more than one character
    pwd = getpass.getpass(prompt="Pass: ", echo_char="**")
    ```

    Might raise a `TypeError` or internal error due to invalid `echo_char`.

---

## Example

```python
# Example 1: Basic usage
import getpass

pwd = getpass.getpass("Enter your password: ")
print("You entered:", pwd)
```

```python
# Example 2: Using custom prompt and stream
import getpass
import sys

pwd = getpass.getpass(prompt="Please enter secret: ", stream=sys.stderr)
print("Secret is:", pwd)
```

```python
# Example 3: Using echo_char (Python 3.14+)
import getpass

pwd = getpass.getpass("Enter pin: ", echo_char='*')
print("PIN entered:", pwd)
```

---

## Best Practices

* Use `getpass.getpass` instead of `input()` when prompting for a password or other secret input in a terminal-based program.
* Ensure your script is executed in a real terminal (TTY) rather than an IDE console or redirected input, because some environments cannot disable echo and will fallback to visible input.
* Do **not** display the returned password unless strictly necessary: handling and storing secrets should follow security best practices (e.g., hashing, not logging, minimal exposure).
* Use a meaningful prompt string so the user knows what they are entering (e.g., “Enter admin password: ”).
* Consider fallback when input is not a TTY (e.g., detect `sys.stdin.isatty()` and ask user to input via alternative method).
* Be aware of version-specific features: `echo_char` is only available from Python 3.14 onward.

---

## Common Pitfalls

* Running `getpass.getpass()` in IDEs (e.g., Visual Studio Code integrated terminal or editors) where standard input is not a true terminal/TTY. In such cases, password may still echo or the prompt may behave unexpectedly.
* Naming your script `getpass.py` (or having a file named `getpass.py` in current directory) causes name-shadowing of the standard library module, leading to `TypeError: 'module' object is not callable`.
* Assuming `echo_char` is available in earlier versions of Python. If you try to use `echo_char` on Python < 3.14 you’ll get a `TypeError` or it will be ignored.
* Expecting full line editing (backspace, Ctrl-U, etc) when `echo_char` is used or when non-canonical mode is engaged: on Unix systems using `echo_char` disables normal line editing behavior.
* Assuming `getpass.getpass()` works when input is piped (`echo "secret" | python script.py`); often it fails or falls back and may echo input, or raise `OSError`.

---
