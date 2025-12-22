# `bcrypt`

Hash a password:

```python
import bcrypt

password = b"super_secret_password"

hashed = bcrypt.hashpw(password, bcrypt.gensalt())
# hashed contains the algorithm identifier, the work factor, the salt, and the hash.

print(hashed)
```

Verify a password:

```python
import bcrypt

password = b"super_secret_password"
stored_hash = b"$2b$12$KIX0zL8..."

if bcrypt.checkpw(password, stored_hash):
    print("Password is valid")
else:
    print("Invalid password")
```
