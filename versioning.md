# Versioning

**Format**: MAJOR.MINOR.PATCH

```
1.2.3
│ │ │
│ │ └─ PATCH: Bug fixes, tiny changes (backwards compatible)
│ └─── MINOR: New features (backwards compatible)
└───── MAJOR: Breaking changes (not backwards compatible)
```

**Examples**:

+ `0.1.0` → First usable version (still in development)
+ `0.1.1` → Fixed a bug
+ `0.2.0` → Added a new feature
+ `1.0.0` → First stable release!
+ `1.0.1` → Bug fix in stable version
+ `1.1.0` → New feature added to stable version
+ `2.0.0` → Breaking change (users may need to update their code)

Update the version in `pyproject.toml`.

---
