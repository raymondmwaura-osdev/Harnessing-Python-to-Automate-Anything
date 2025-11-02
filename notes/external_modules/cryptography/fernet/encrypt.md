# `Fernet.encrypt`

## Overview

The `encrypt()` method of the Fernet class takes a plaintext message (as bytes) and produces a secure token (also bytes) that encapsulates the encrypted payload, a timestamp, and an authenticity tag. Fernet ensures **confidentiality**, **integrity**, and **authenticity** of the data: only someone with the correct key can decrypt, and modifications to the token are detected.

---

## Parameters

The signature of the method is approximately:

```python
encrypt(self, data: bytes) -> bytes
```

* **data**: A `bytes` object containing the plaintext to be encrypted. The method requires the input to be bytes; passing a `str` without encoding will raise an error.
* No additional mandatory parameters are required for `encrypt()` (unlike `decrypt()` which may accept a TTL parameter).
* The method uses the key that the Fernet instance was initialized with (via `Fernet(key)`).
* Internally, `encrypt()` will: generate a fresh IV/non-secret randomness, embed the current timestamp, perform AES encryption, compute an HMAC to cover the version, timestamp, IV and ciphertext, then encode the whole package as URL-safe Base64.

---

## Return Value

* **Type:** `bytes`
* The method returns a bytes object, representing a Fernet token which is a URL-safe Base64-encoded string (in bytes form). This token contains the encrypted data plus metadata (version, timestamp, IV, HMAC).
* The returned token can be transmitted, saved to file, or embedded in text contexts because it is Base64 URL-safe.
* Later you pass this token to `decrypt()` (with the correct key) to recover the original plaintext bytes.

---

## Example

```python
from cryptography.fernet import Fernet

# Step 1: Generate a key
key = Fernet.generate_key()

# Step 2: Create a Fernet instance
f = Fernet(key)

# Step 3: Plaintext message (bytes)
plaintext = b"Confidential message."

# Step 4: Encrypt the plaintext
token = f.encrypt(plaintext)
print("Encrypted token:", token)

# (Later) Decrypt the token to recover plaintext
decrypted = f.decrypt(token)
print("Decrypted plaintext:", decrypted.decode('utf-8'))
```

**Explanation:**

* `generate_key()` produces a secure random key (Base64 encoded).
* `Fernet(key)` constructs a Fernet object.
* `.encrypt(plaintext)` returns a token (bytes) which you print.
* `.decrypt(token)` returns the original plaintext bytes, which you decode to a string for user display.
* If the token were tampered, or if a different key were used, `decrypt()` would raise an exception and not silently produce wrong output.

---
