# Understanding Encryption, Key Derivation, and Fernet

This tutorial introduces the theoretical foundations necessary to implement password-based file encryption using the **`cryptography`** library and **Fernet**.
It is designed for readers new to encryption, key derivation, and general cryptographic concepts.

---

## Fundamental Concepts in Cryptography

### The Goals of Cryptography

Cryptography is the study and practice of securing communication and information from unauthorized access or alteration. Its major goals are:

* **Confidentiality**; Ensuring data can only be read by authorized entities.
* **Integrity**; Ensuring data has not been altered during storage or transmission.
* **Authenticity**; Ensuring that the data originates from a legitimate source.

A secure encryption system ideally achieves all three simultaneously.

### Symmetric and Asymmetric Encryption

Cryptographic systems fall into two main categories:

* **Symmetric encryption**: Uses the same key for encryption and decryption. It is computationally efficient and suitable for encrypting data stored locally, such as files.
* **Asymmetric encryption**: Uses a key pair; one public, one private. It is slower but ideal for secure key exchange and digital signatures.

For most password-based encryption applications, symmetric encryption is preferred.

### Block Ciphers, Modes of Operation, and Initialization Vectors

A **block cipher** (e.g., AES) encrypts fixed-size blocks of data using a secret key. To handle data of arbitrary length, **modes of operation** are used, such as:

* **CBC (Cipher Block Chaining)**
* **CTR (Counter Mode)**
* **GCM (Galois/Counter Mode)**

Each mode defines how successive blocks are processed.

An **Initialization Vector (IV)** or **nonce** (number used once) ensures that encrypting identical plaintexts results in different ciphertexts. It introduces randomness and prevents pattern analysis. Reusing an IV with the same key can severely compromise security.

A **salt**, in contrast, is used not during encryption but in *key derivation*. It ensures that identical passwords produce different cryptographic keys.

### Authenticated Encryption (AE and AEAD)

**Authenticated Encryption (AE)** combines confidentiality and integrity in a single process. It ensures that any modification to ciphertext will cause decryption to fail.

**Authenticated Encryption with Associated Data (AEAD)** extends this idea by allowing some data (e.g., metadata) to remain unencrypted while still being integrity-protected.

AE and AEAD are essential for secure storage, as they prevent both unauthorized reading and undetected modification of encrypted data.

---

## Key Derivation: Converting Passwords into Cryptographic Keys

### Motivation

Human-chosen passwords are typically short and predictable, making them unsuitable as cryptographic keys.
A **Key Derivation Function (KDF)** transforms a password into a fixed-length, high-entropy key suitable for use in encryption algorithms.

### What is a Key Derivation Function (KDF)?

A **KDF** is an algorithm that takes a password (or other input) and produces one or more cryptographically strong keys.
It often uses parameters such as **salt**, **iteration count**, and **memory cost** to make brute-force attacks computationally expensive.

The KDF’s purposes include:

* “Stretching” weak passwords into strong keys.
* Ensuring different salts lead to unique keys even for identical passwords.
* Making brute-force guessing significantly slower.

### Important Parameters

* **Salt**:
  A random, non-secret value stored alongside the ciphertext. It ensures that the same password generates different derived keys across different encryption sessions.

* **Iteration Count or Cost Factor**:
  Specifies how computationally expensive it is to derive a key. Increasing the number of iterations slows both legitimate derivations and brute-force attacks.

* **Output Length**:
  Determines how many bits of key material to generate. For AES-256 encryption, for example, the derived key must be 256 bits long.

* **Input Keying Material (IKM)**:
  The raw password or passphrase provided by the user.

### Commonly Used KDFs

1. **PBKDF2 (Password-Based Key Derivation Function 2)**
   Uses repeated hashing (often with HMAC-SHA256). Well-established and widely available.
2. **scrypt** and **Argon2**
   Designed to be *memory-hard*, meaning they use large amounts of RAM to resist parallel brute-force attacks using GPUs or ASICs.
3. **HKDF (HMAC-based Key Derivation Function)**
   Used to derive multiple keys from an existing high-entropy secret, typically in protocols rather than password-based applications.

### Why Not Use a Hash Directly?

Directly hashing a password (e.g., with SHA-256) is insecure because:

* Human passwords have low entropy and can be brute-forced quickly.
* The same password always yields the same hash, allowing precomputed dictionary attacks.
* A plain hash does not include a salt or cost factor.

A KDF adds randomness and computational cost, dramatically improving security.

---

## The Encryption Process

### Deriving the Encryption Key

When encrypting a file using a password:

1. Generate a random **salt** and store it with the encrypted file.
2. Use a **KDF** (e.g., PBKDF2) with the password and salt to derive a fixed-length key.
3. Use this key with a symmetric encryption algorithm (such as AES via Fernet).
4. The result is a ciphertext that can be decrypted only by re-deriving the same key using the same password and salt.

If the password or salt changes, the derived key will differ, and decryption will fail.

### Encryption and Decryption Workflow

**Encryption process:**

1. The plaintext (e.g., sensitive data or credentials) is taken as input.
2. A symmetric encryption algorithm encrypts it using the derived key.
3. The algorithm outputs ciphertext that includes both encrypted data and an authentication tag.
4. The salt (and possibly metadata) is stored with the ciphertext.

**Decryption process:**

1. The user enters the password.
2. The system reads the salt from the file and re-derives the key using the same KDF parameters.
3. The ciphertext is decrypted using the derived key.
4. If the authentication tag verification fails, the data has been altered or the password is incorrect.

### File Format and Storage Considerations

A secure encrypted file format typically includes:

* A **header** with metadata (e.g., version or algorithm identifiers).
* The **salt** (stored in plaintext).
* The **ciphertext**, including authentication tag or checksum.

Security practices:

* File permissions should restrict access to the encrypted data (e.g., user read/write only).
* The password or derived key must never be stored in plaintext.
* All encryption and decryption should occur in memory only.

### Why Use Fernet

**Fernet**, provided by the `cryptography` Python library, is a high-level encryption standard designed for ease of use and safety.
It is based on AES in CBC mode with HMAC for authentication, providing **authenticated encryption**.

Benefits of using Fernet:

* Provides confidentiality, integrity, and authenticity.
* Manages IVs and HMAC verification automatically.
* Uses standard cryptographic primitives with safe defaults.
* Prevents common mistakes such as reusing IVs or omitting authentication.

Using Fernet avoids many pitfalls associated with implementing encryption primitives manually.

---
