# `Fernet.decrypt`

## Overview

The `decrypt()` method of the Fernet class is used to convert a Fernet token (ciphertext produced by `encrypt()`) back into the original plaintext, provided the correct key is used and the token is valid (i.e., not tampered, not expired if a TTL is specified).
It ensures **confidentiality** (only the holder of the correct key can decrypt), **integrity** (any modification to the token will cause decryption to fail), and optionally **freshness** (if a TTL parameter is used then tokens older than that limit will be rejected).

---

## Parameters

The typical signature of the `decrypt()` method is approximately:

```python
decrypt(self, token: bytes, ttl: Optional[int] = None) -> bytes
```

Here are the parameters described:

* **`token`** (type: `bytes`)

  * Description: A Fernet token (i.e., the result of a prior `encrypt()` call) encoded in a URL-safe Base64 form, passed as bytes. This is the ciphertext plus metadata (timestamp, IV, HMAC) that needs to be decrypted.
  * Notes: If a string is passed rather than bytes, one must first convert it to bytes (for example via `token.encode('ascii')`) to avoid errors.

* **`ttl`** (type: `int`, optional, default: `None`)

  * Description: An optional parameter representing “time to live” in seconds. If provided, the method will check the timestamp embedded in the token and raise an error if the token is older than `ttl` seconds relative to when it was encrypted.
  * Notes: If `ttl` is `None` (the default), then the token age is *not* checked and any age is accepted (so long as the token is valid and key matches).

---

## Return Value

* **Type**: `bytes`
* **Description**: On success, `decrypt()` returns the original plaintext bytes (the same data that was passed into `encrypt()` before encryption).
* **Error behavior**: If decryption fails (for example because the key is incorrect, the token has been tampered with, the TTL expired, or the token format is invalid), the method raises a `cryptography.fernet.InvalidToken` exception (or an equivalent error indicating invalid token).

---

## Example

Here is a simple usage example of `decrypt()`:

```python
from cryptography.fernet import Fernet

# Step 1: Generate a key (for demonstration)
key = Fernet.generate_key()

# Step 2: Create a Fernet object
f = Fernet(key)

# Step 3: Encrypt a message (plaintext as bytes)
plaintext = b"Secret message to decrypt."
token = f.encrypt(plaintext)

# Step 4: Decrypt the token
decrypted_bytes = f.decrypt(token)

# Step 5: Convert bytes to string if desired
decrypted_text = decrypted_bytes.decode('utf-8')
print("Decrypted message:", decrypted_text)
```

**Explanation:**

* A new key is generated with `generate_key()`.
* A Fernet instance is created using that key.
* The plaintext (bytes) is encrypted and a token (bytes) is produced.
* `decrypt(token)` uses the same key to recover the plaintext bytes.
* Finally the bytes are decoded into a human-readable string for display.

If we provided a TTL parameter, it might look like:

```python
# Attempt decryption only if token is no older than 3600 seconds (1 hour)
decrypted_bytes = f.decrypt(token, ttl=3600)
```

If the token were older than 1 hour, `decrypt()` would raise an exception instead of returning plaintext.

---
