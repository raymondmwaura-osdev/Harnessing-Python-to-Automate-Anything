# `Fernet`

## Overview

Fernet is a high-level symmetric encryption class that ensures **confidentiality** (data cannot be read without the key) and **integrity/authenticity** (data cannot be altered undetected) of messages.
With Fernet you supply a secret key and use the same key to encrypt and decrypt data. It builds on established primitives (AES + HMAC) but presents a safe, simple API so you avoid many pitfalls of handling encryption directly.

---

## Parameters

When you create a `Fernet` object, you supply a single required parameter:

* **key** (type: `bytes`): The secret key used for both encryption and decryption. It must be a suitable length (32 url-safe Base64-encoded bytes) and kept secret.

There are no additional required parameters at initialization time. Internally, the key wraps the symmetric cipher and authentication primitives. Optional/run-time parameters are supplied to methods rather than the constructor.

---

## Return Value

* The constructor `Fernet(key)` returns an instance of the `Fernet` class.
* Its methods (`encrypt()`, `decrypt()`) produce or consume values.
* `encrypt(data: bytes) → bytes`: Returns a token (ciphertext) of type `bytes` which is URL-safe Base64-encoded and contains the encrypted data plus integrity/authentication metadata.
* `decrypt(token: bytes, ttl: Optional[int]=None) → bytes`: Takes a token and returns the original plaintext `bytes` if the token is valid (key matches, token not expired if TTL provided, data not tampered). If verification fails, it raises an exception.

---

## Methods

Here are the major methods of the `Fernet` class:

* `@classmethod generate_key() -> bytes`
  Generates a new random key suitable for use with `Fernet`. Returns a `bytes` object (typically Base64-encoded). This key must be stored and protected, since loss of the key means loss of the ability to decrypt.
* `__init__(self, key: bytes)`
  Constructor. Accepts the key bytes and initializes the `Fernet` instance.
* `encrypt(self, data: bytes) -> bytes`
  Encrypts the given plaintext bytes `data`. Returns a token (bytes) representing the encrypted payload, timestamp, and integrity tag.
* `decrypt(self, token: bytes, ttl: int = None) -> bytes`
  Decrypts the token. If `ttl` is provided, it also checks that the token is no older than `ttl` seconds (based on the timestamp embedded). Returns plaintext bytes if successful; otherwise raises an error (invalid token, expired, tampered).

---

## Example

Below is a simple example demonstrating key-generation, encryption, and decryption using `Fernet`.

```python
from cryptography.fernet import Fernet

# Step 1: Generate a key
key = Fernet.generate_key()

# Step 2: Create a Fernet instance
f = Fernet(key)

# Step 3: Encrypt a message (plaintext as bytes)
plaintext = b"Secret message goes here."
token = f.encrypt(plaintext)
print("Encrypted token:", token)

# Step 4: Decrypt the token
decrypted = f.decrypt(token)
print("Decrypted message:", decrypted.decode('utf-8'))
```

**Explanation:**

* We generate a strong random key with `generate_key()`.
* We instantiate `Fernet` with that key.
* We call `encrypt()` on plaintext bytes; we get back an opaque token (bytes).
* We call `decrypt()` on the token; we receive back the original plaintext bytes, which we decode to a string for display.

---
