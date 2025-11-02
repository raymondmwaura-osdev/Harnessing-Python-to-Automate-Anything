# `str.encode`

## Overview

The method transforms the sequence of Unicode code points held by a Python `str` into a sequence of bytes according to a character encoding (for example UTF-8 or ASCII). It is the standard way in Python to produce binary data from text for file I/O, networking, hashing, or other binary protocols.

---

## Signature and parameters

**Signature (informal):** `s.encode(encoding=None, errors='strict')`

* `encoding`; Optional. The name of the character encoding to use (e.g. `'utf-8'`, `'ascii'`, `'latin-1'`, etc.). If omitted the platform/Python default is used; in modern Python 3 code `utf-8` is the usual default. The encoder used is chosen from Python’s codec registry.
* `errors`; Optional. Error-handling policy when a character cannot be represented in the target encoding. Common policies:

  * `'strict'`; raise `UnicodeEncodeError` (default).
  * `'ignore'`; skip unencodable characters.
  * `'replace'`; substitute a replacement marker (e.g. `?` or a sequence defined by the codec).
  * Other handlers exist (e.g. `'backslashreplace'`, `'xmlcharrefreplace'`, `'surrogateescape'`). Error handlers are implemented via the codec registry.

---

## Return value

The method returns a `bytes` object containing the encoded byte sequence. The length of the resulting `bytes` depends on the encoding (for example UTF-8 uses 1-4 bytes per code point).

---

## Errors and exceptions

If `errors='strict'` (the default) and the string contains characters that the chosen encoding cannot represent, Python raises `UnicodeEncodeError`. Choosing a tolerant `errors` policy avoids the exception at the cost of data loss or transformation.

---

## Example

```python
# original Unicode string
s = "Café — π"

# default encoding (UTF-8) -> bytes
b_utf8 = s.encode()
# b_utf8 == b'Caf\xc3\xa9 \xe2\x80\x94 \xcf\x80'

# explicit encoding
b_latin1 = s.encode('latin-1', errors='replace')
# latin-1 cannot represent '—' or 'π', so with 'replace' they are substituted
# b_latin1 == b'Caf\xe9 ???'  (actual replacement bytes depend on handler)

# handle unencodable characters by skipping them
b_ascii = s.encode('ascii', errors='ignore')
# b_ascii == b'Caf '  (non-ASCII characters removed)
```

Notes on the example: the default `encode()` call uses UTF-8 in common Python 3 installations. Encoded byte literals are shown in Python `bytes` notation. Choosing `errors` changes whether an exception is raised or how unencodable characters are transformed.

---

## Practical guidance (concise)

* Use `'utf-8'` unless you must interoperate with legacy systems. ([realpython.com][4])
* Prefer explicit `encoding` and an explicit `errors` policy in library or network code to avoid platform-dependent behavior.

---
