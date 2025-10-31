# PBKDF2HMAC; Password-Based Key Derivation Function

## Overview

`PBKDF2HMAC` is a class provided by the `cryptography` library in Python.
It implements the **Password-Based Key Derivation Function 2 (PBKDF2)** using **HMAC (Hash-Based Message Authentication Code)** as its pseudorandom function (PRF).

PBKDF2 is defined in [RFC 8018 (PKCS #5 v2.1)](https://www.rfc-editor.org/rfc/rfc8018) and is widely used to transform a **human-readable password** into a **cryptographically strong key**.
The resulting key can then be used in symmetric encryption algorithms such as AES or Fernet.

This mechanism ensures that even if users choose weak or predictable passwords, the derived keys have properties suitable for secure cryptographic operations.

---

## Parameters

The `PBKDF2HMAC` class is initialized with several required parameters that control its behavior and security properties:

### algorithm

* **Type:** Instance of a hash algorithm from `cryptography.hazmat.primitives.hashes` (e.g., `hashes.SHA256()`).
* **Description:**
  Specifies the underlying hash function used in the HMAC-based pseudorandom function (PRF).
  Common choices include SHA1 (legacy), SHA256, or SHA512.
* **Impact:**
  Determines the strength and output length of the derived key material. Stronger algorithms (e.g., SHA256 or SHA512) are recommended.

---

### length

* **Type:** `int`
* **Description:**
  The desired length of the derived key, in bytes.
* **Impact:**
  The derived key will be exactly this many bytes long.
  For example, AES-256 requires a 32-byte (256-bit) key.

---

### salt

* **Type:** `bytes`
* **Description:**
  A random, non-secret value used to ensure that identical passwords produce unique derived keys.
  The salt should be randomly generated for each derivation and stored (unencrypted) alongside the ciphertext.
* **Impact:**
  Prevents precomputed dictionary and rainbow-table attacks.
  Reusing a salt weakens security but does not immediately compromise confidentiality.

---

### iterations

* **Type:** `int`
* **Description:**
  The number of times the HMAC function is iteratively applied during key derivation.
* **Impact:**
  Increasing this value slows down both legitimate key derivation and brute-force guessing attacks.
  As of current security recommendations, values of **≥100,000 iterations** are considered minimal, though modern recommendations often exceed **300,000**.

---

### backend

* **Type:** Instance of `cryptography.hazmat.backends.interfaces.HashBackend`
* **Description:**
  Provides the cryptographic backend implementation used to perform hashing operations.
  The most common and default option is:

  ```python
  from cryptography.hazmat.backends import default_backend
  ```
* **Impact:**
  In typical use, this parameter should always be `default_backend()` unless a specific cryptographic backend is required.

---

### Optional Parameters

The `PBKDF2HMAC` constructor does not include additional optional parameters.
However, it is important to note that **the password itself is not passed at initialization**; it is supplied later when calling the `.derive()` or `.verify()` methods.

---

## Return Value

`PBKDF2HMAC` is a **class**, not a function.
When instantiated, it returns a configured **PBKDF2HMAC object** that can perform key derivation operations.

The **derived key** is obtained by calling its `.derive()` method.
The output of `.derive()` is a sequence of raw bytes representing the cryptographic key.
These bytes can be used directly by cryptographic algorithms or encoded (e.g., with Base64) for storage or use in APIs that expect text-based keys.

---

## Methods

### `derive(password: bytes) -> bytes`

* **Description:**
  Performs the key derivation operation.
  Takes a password as input and produces the derived key according to the PBKDF2 parameters (algorithm, salt, iterations, length).
* **Parameters:**

  * `password`: The input password, provided as a `bytes` object.
* **Return Value:**
  A `bytes` object representing the derived key of the specified length.
* **Behavior:**

  * The same parameters (salt, iterations, algorithm) and the same password will always produce the same key.
  * Different salts will produce different keys, even if the password is identical.

### `verify(password: bytes, expected_key: bytes) -> None`

* **Description:**
  Verifies whether a given password would generate a derived key matching the provided `expected_key`.
* **Parameters:**

  * `password`: The password to verify, provided as bytes.
  * `expected_key`: The known correct derived key, also provided as bytes.
* **Return Value:**

  * Returns `None` if the password and expected key match.
  * Raises `InvalidKey` exception if they do not match.
* **Use Case:**
  Useful for verifying user credentials or ensuring that an entered password corresponds to an existing key without directly decrypting any data.

---

## Example

The following example demonstrates how to use `PBKDF2HMAC` to derive a 256-bit key from a password using SHA-256, and then how to verify it.

```python
from cryptography.hazmat.primitives.kdf.pbkdf2 import PBKDF2HMAC
from cryptography.hazmat.primitives import hashes
from cryptography.hazmat.backends import default_backend
import os
import base64

# Step 1: Generate a random salt
salt = os.urandom(16)  # 128-bit salt

# Step 2: Configure PBKDF2HMAC
kdf = PBKDF2HMAC(
    algorithm=hashes.SHA256(),
    length=32,                # 32 bytes = 256 bits
    salt=salt,
    iterations=390000,        # modern secure iteration count
    backend=default_backend()
)

# Step 3: Derive the key from a password
password = b"strongpassword123"
key = kdf.derive(password)

# Step 4: Encode key in Base64 for safe storage or use with Fernet
encoded_key = base64.urlsafe_b64encode(key)
print("Derived Key:", encoded_key)

# Step 5: Verify the password later
try:
    kdf.verify(b"strongpassword123", key)
    print("Password verified successfully.")
except Exception:
    print("Password verification failed.")
```

### Explanation of Example Steps

1. **Salt Generation:**
   A random salt ensures uniqueness across different derivations.
2. **KDF Configuration:**
   Defines the hash algorithm, desired key length, salt, and iteration count.
3. **Key Derivation:**
   Converts the password into a secure cryptographic key.
4. **Encoding:**
   Converts binary output to Base64 for text-safe usage.
5. **Verification:**
   Checks whether a provided password regenerates the same key.

---

## Security Recommendations

* Use a **unique random salt** for each derivation (at least 128 bits).
* Choose **SHA256** or stronger as the hash algorithm.
* Use **≥300,000 iterations** for current hardware.
* Store the **salt and iteration count** with the encrypted data (in plaintext).
* Never store or transmit the derived key in plaintext unless absolutely necessary.

---
