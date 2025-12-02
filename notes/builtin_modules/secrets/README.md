# `secrets`

## What is `secrets`

* The `secrets` module is part of the Python standard library (added in Python 3.6).
* Its purpose is to generate *cryptographically secure* random numbers and tokens, suitable for sensitive data such as passwords, session tokens, reset links, authentication credentials, and other “secrets.”
* Unlike the default pseudo-random generator provided by the `random` module (which is intended for simulations, games, etc.), `secrets` bases its randomness on the most secure source available from the underlying operating system; e.g. reading from `/dev/urandom`, or equivalent system-provided entropy sources.

---

## Core Interface

The `secrets` module offers both lower-level random-number APIs and higher-level token generation utilities. Key components:

+ `secrets.SystemRandom`; A random-number generator that wraps the OS’s secure randomness source; akin to `random.SystemRandom`.
+ `secrets.choice(seq)`; Select a random element from a non-empty sequence (e.g. list, string) in a cryptographically secure way.
+ `secrets.randbelow(n)`; Return a random integer in the range [0, n); i.e. from 0 up to but not including `n`.
+ `secrets.randbits(k)`; Return a non-negative integer containing *k* random bits. Useful when you need a big random integer with specified bit-length.
+ `secrets.token_bytes([nbytes])`; Return a random byte string of length `nbytes`. If `nbytes` is omitted or `None`, a sensible default is used.
+ `secrets.token_hex([nbytes])`; Return a random text string; the bytes are hex-encoded (two hex digits per byte). Good for identifiers, tokens, etc.
+ `secrets.token_urlsafe([nbytes])`; Return a random string, base64-encoded in a URL-safe manner; often used for secure tokens embedded in URLs (e.g. password reset links).
+ `secrets.compare_digest(a, b)`; Compare two strings or bytes-like objects using a “constant-time” algorithm, helping prevent timing attacks.

---

## Example Usage Patterns

Below are some common patterns and idioms when using `secrets` in real applications.

```python
import secrets

# Generate a URL-safe token, e.g. for password reset link
token = secrets.token_urlsafe(32)
print("Reset link token:", token)

# Generate a random 16-byte token (raw bytes)
raw = secrets.token_bytes(16)
print("Raw bytes:", raw)

# Generate a secure random integer < 1000
n = secrets.randbelow(1000)
print("Random integer:", n)

# Choose a random element from a list
options = ['apple', 'banana', 'cherry']
choice = secrets.choice(options)
print("Random choice:", choice)
```

### Example: Secure Password Generation

```python
import secrets, string

alphabet = string.ascii_letters + string.digits  # e.g. letters + digits
password = ''.join(secrets.choice(alphabet) for _ in range(12))
print("Secure password:", password)
```

More elaborate example (requiring at least one lowercase, one uppercase, at least 3 digits):

```python
import secrets, string

alphabet = string.ascii_letters + string.digits
while True:
    pwd = ''.join(secrets.choice(alphabet) for _ in range(10))
    if (any(c.islower() for c in pwd)
        and any(c.isupper() for c in pwd)
        and sum(c.isdigit() for c in pwd) >= 3):
        break
print("Strong password:", pwd)
```

This is more secure than using the `random` module, because each character is selected from a cryptographically secure source.

---

## Security Considerations & Best Practices

* Use `secrets` instead of `random` when generating anything security-sensitive (passwords, tokens, keys). The default `random` module uses a deterministic algorithm that may be predictable if the seed becomes known.
* For tokens that will be used in security contexts (e.g. session tokens, password reset links), use sufficiently large entropy.
* When comparing secrets (e.g. user-provided token vs stored token), use `secrets.compare_digest()` rather than naive `==`. This helps mitigate timing attacks.
* Avoid storing plaintext passwords or tokens; instead, store salted password hashes, or store tokens only long enough for single use. The `secrets` module helps generate tokens, but secure storage practices remain essential.

---

## When and When Not to Use `secrets`

Use `secrets` when:

* You need unpredictability and cryptographic-grade randomness (passwords, tokens, keys, salts).
* You want to generate tokens suitable for embedding in URLs (e.g. reset links, password recovery), or tokens for session management.
* You want to pick random elements or numbers in a way that cannot be predicted or manipulated by an attacker.

Avoid or use simpler modules when:

* You are doing non-security-related randomness (e.g. shuffling a list for UI display, simulation, games); in that case performance and reproducibility may matter more than cryptographic security, and the `random` module may suffice.
* You do not need unpredictable randomness; or if you deliberately want reproducible randomness (e.g. seeded randomness for tests).

---
