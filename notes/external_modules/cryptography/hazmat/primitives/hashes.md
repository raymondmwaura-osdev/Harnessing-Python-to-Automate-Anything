# `cryptography.hazmat.primitives.hashes`

### Overview

The module `cryptography.hazmat.primitives.hashes` is part of the Python `cryptography` library and provides implementations of **cryptographic hash functions** (also called message-digests). A cryptographic hash function takes an arbitrary block of data and computes a fixed-size digest (a “fingerprint”) such that it is computationally infeasible to find two distinct inputs that yield the same digest (collision resistance), or to produce an input that yields a given digest (preimage resistance).
Hash functions play a central role in cryptography: for message integrity (detecting changes), for key derivation (stretching passwords), for digital signatures, for random-oracle style constructions, and for fingerprinting data.
In this module you can perform hashing operations, incremental updates, and obtain digests for data streams.

---

### Parameters (in main Hash API)

In the `hashes` module you typically work with the class `Hash` (or `HashContext`). Its constructor takes parameters:

* `algorithm`: An instance of a subclass of `HashAlgorithm` (for example `hashes.SHA256()`, `hashes.BLAKE2b(…)`, etc). This selects which hash function will be used.
* `backend` (optional): An object implementing the `HashBackend` interface that performs the actual computation. If omitted, the library’s default backend is used.

Once constructed you call methods (see below) like `update(data)` and `finalize()` to compute the hash.

---

### Return Value

* The `Hash.finalize()` method returns a `bytes` object representing the digest (hash) of all data that has been supplied via `update()`.
* After `finalize()` is called, the `Hash` context is finalised and cannot accept further `update()` calls (calling them will raise `AlreadyFinalized`).

---

### Methods

Here are the primary methods of the `Hash` (or `HashContext`) class in the module:

* `update(data: bytes) -> None`
  Adds the bytes `data` to the hash input. You can call this method multiple times to stream data. Raises `TypeError` if `data` is not `bytes`. After `finalize()` has been called, `update()` will raise `AlreadyFinalized`.
* `finalize() -> bytes`
  Completes the hashing operation and returns the digest. After it is called, the `Hash` object is finalized and cannot be reused.
* `copy() -> Hash`
  Creates and returns a duplicate of the current hash context (so you can derive an intermediate digest and continue updating the original). The copy is independent of the original. Calling `copy()` after `finalize()` raises `AlreadyFinalized`.

Additionally, algorithm classes (e.g., `SHA256`, `SHA3_512`, `BLAKE2b`) each define attributes such as `digest_size` (the fixed length of their output) and `name`.

---

### Example

```python
from cryptography.hazmat.primitives import hashes
from cryptography.hazmat.backends import default_backend

# Create a hash context for SHA-256
digest = hashes.Hash(hashes.SHA256(), backend=default_backend())

# Update with data in multiple parts
digest.update(b"Hello ")
digest.update(b"world!")

# Obtain the final digest
result = digest.finalize()
print("SHA-256 digest:", result.hex())
```

In this example:

* A new hash context is created specifying the SHA256 algorithm.
* Two `update()` calls feed the data in parts.
* `finalize()` produces the digest of the combined data.
* The digest is then printed in hexadecimal form.

---
