# `base64`

## Overview

The `base64` module in Python provides functions for **encoding binary data into an ASCII-text representation** and decoding such representations back into binary form.
This encoding scheme (Base64) is widely used when one needs to **transmit or store binary data** (such as images, encryption keys or binary files) through systems that are designed to handle text (for example emails, JSON, XML or URL parameters).
It is important to emphasise: Base64 is an encoding mechanism; **not encryption**. It does *not* by itself provide confidentiality or security, but simply transforms data into a representable textual format.

---

## Most-Used Functions

Here are some of the typical functions in the `base64` module and what they are used for:

* `base64.b64encode(data: bytes) -> bytes`
  Encodes the given binary data (a `bytes` object) into Base64-encoded bytes.
* `base64.b64decode(data: bytes or str, altchars: bytes = None, validate: bool = False) -> bytes`
  Decodes a Base64-encoded bytes or ASCII string back to its original binary form.
* `base64.urlsafe_b64encode(data: bytes) -> bytes`
  Similar to `b64encode` but uses a “URL-safe” alphabet (substituting certain characters) so the encoded form may safely appear in URLs or filenames.
* `base64.urlsafe_b64decode(data: bytes or str) -> bytes`
  Decodes data that was encoded via `urlsafe_b64encode`, handling the URL-safe alphabet.
* `base64.b32encode()`, `base64.b16encode()`, etc.
  Some additional functions support other base-encodings (Base16, Base32) though the most common usage is Base64.

---
