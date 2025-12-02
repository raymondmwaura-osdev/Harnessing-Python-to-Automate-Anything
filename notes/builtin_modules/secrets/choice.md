# `secrets.choice`

## Overview

The function `secrets.choice` selects and returns a single, random element from a non-empty sequence (e.g. a list, tuple, string) in a way that is cryptographically secure; meaning the randomness originates from the best available source in the underlying operating system, not a predictable pseudo-random generator.

This makes `secrets.choice` suitable for security-sensitive tasks such as generating random passwords, tokens, session identifiers, or any case where unpredictability is critical. It integrates seamlessly with standard Python data types (sequences) and can replace the `choice` function from the `random` module in contexts requiring stronger randomness guarantees.

---

## Syntax

```python
secrets.choice(sequence)
```

---

## Parameters

* `sequence`

  * **Type:** Any non-empty sequence type (e.g. list, tuple, string).
  * **Description:** The function will select one random element from this sequence.
  * **Default Value:** None (parameter is required).
  * **Constraints:** The sequence must be non-empty. If the sequence is empty, the behavior is undefined (typically raises an error similar to standard `choice` functions).

---

## Return Value

* **Type:** Same as the element type of the input sequence.
* **Description:** Returns a single element from the input sequence, chosen at random with uniform distribution (i.e. each element has equal probability) assuming a fair random source.
* **Special Notes:** The returned element is one of the elements in the original sequence. The function does *not* return a list or generator; just one element.

---

## Errors and Exceptions

* **Exception Type:** `IndexError`

  * **Cause:** If the provided sequence is empty.
  * **Example:**

    ```python
    import secrets
    secrets.choice([])  # Raises IndexError because the sequence is empty
    ```

* **Exception Type:** `TypeError`

  * **Cause:** If the argument is not a sequence (e.g. `None`, integer, or other non-sequence object).
  * **Example:**

    ```python
    import secrets
    secrets.choice(123)  # TypeError: ’int’ object is not subscriptable (or similar)
    ```

---

## Example

```python
import secrets

# Example 1: Choose a random character from a string
alphabet = 'abcdefghijklmnopqrstuvwxyz'
letter = secrets.choice(alphabet)
print(letter)  # e.g. 'q'

# Example 2: Choose a random element from a list
colors = ['red', 'green', 'blue', 'yellow']
color = secrets.choice(colors)
print(color)  # randomly one of 'red','green','blue','yellow'

# Example 3: Use secrets.choice for secure password generation
import string
alphabet = string.ascii_letters + string.digits + string.punctuation
password = ''.join(secrets.choice(alphabet) for _ in range(12))
print(password)  # e.g. 'aB8#x9*Kz!3Q'
```

---

## Best Practices

* Use `secrets.choice` instead of `random.choice` when generating sensitive data (passwords, security tokens, cryptographic keys), because `secrets` draws randomness from a secure system source rather than a deterministic pseudo-random generator.
* For repeated random picks (e.g. building a random string or password), avoid reseeding or reconstructing the sequence on every iteration; keep the candidate set (e.g. alphabet) constant and perform multiple calls to `secrets.choice`.
* Maintain readability by clearly naming the candidate sequence (e.g. `alphabet`, `choices`) and by avoiding overly complex one-liner comprehensions if clarity is more important than brevity.
* Use only when unpredictability / security matters. For non-security-related randomness (e.g. games, simulations), the standard `random` module may suffice.

---

## Common Pitfalls

* Assuming `secrets.choice` can sample multiple items at once (with replacement or without) like `random.choices`. It does **not**; it returns only a single element per call. If multiple picks are needed, you must call it repeatedly.
* Passing non-sequence types (e.g. integer, `None`); results in `TypeError`.
* Passing an empty sequence; results in `IndexError`.
* Believing that using `secrets.choice` alone makes overall application security sufficient. It only provides secure randomness; you still need secure storage (e.g. hashing, token expiry) for sensitive data generated.

---
