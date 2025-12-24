# `pyproject.toml`

There are 3 possible tables in this file:

+ `[build-system]` (required): Declares which build backend to use and which other dependencies are needed to build the project.
    A build backend is a tool that packages the project's source code into an installable package. Examples: `setuptools`(recommended), `hatchling`, `flit_core`, etc. `setuptools` works for most projects. It is the default, well-tested, and handles complex cases.
+ `[project]`: Used to specify your project's basic metadata, such as dependencies, your name, etc.
+ `[tool]`: Contains tool-specific subtables like `[tool.hatch]`, `[tool.mypy]`, etc.

---

## `[build-system]`

+ `build-backend`: Specifies the build backend to use.
+ `requires`: A list of dependencies needed to build the project.

Example:

```toml
[build-system]
requires = ["setuptools >= 77.0.3"]
build-backend = "setuptools.build_meta"
```

---

## `[project]`

Instead of writing values in this field directly, you can let your backend compute the metadata by marking them as dynamic.

```toml
[project]
dynamic = ["version"]
```

Keys and values:

+ `name` (required): The name of your project on PyPI. Cannot be marked as dynamic.
+ `version`: The version of your project.
+ `dependencies`: List project dependencies.
+ `requires-python`: Declare the minimum version of Python supported.
+ `authors` and `maintainers`: Contains a list of people identified by a name and/or an email address.
+ `description`: A one-line description of your project. It is shown as the "headline" of your project page on PyPI, and other places such as lists of search results.
+ `readme`: Specify a file with a longer description of your project. The file's format is automatically detected from its extension. You can also specify the format using `content-type`.
+ `license`: An SPDX license expression consisting of one or more license identifiers.
+ `license-files`: A list of license file glob patterns.
+ `keywords`: This helps PyPI's search box suggest the project when people search for the specified keywords.
+ `classifiers`: A list of PyPI classifiers that apply to the project.

**Subtables**:

+ `[project.optional-dependencies]`: List optional project dependencies.
+ `[project.scripts]`: Install a cli command as part of your package.
+ `[project.gui-scripts]`: Same as `[project.scripts]`. The difference is only relevant in Windows. On Windows, using this prevents a terminal from popping up.
+ `[project.urls]`: A list of URLs associated with the project, displayed on the left sidebar of the project's PyPI page.

**Example**:

```toml
[project]
name = "spam-eggs"
version = "2020.0.0"
dependencies = [
  "httpx",
  "gidgethub[httpx]>4.0.0",
  "django>2.1; os_name != 'nt'",
  "django>2.0; os_name == 'nt'",
]
requires-python = ">= 3.8"
authors = [
  {name = "Pradyun Gedam", email = "pradyun@example.com"},
  {name = "Tzu-Ping Chung", email = "tzu-ping@example.com"},
  {name = "Another person"},
  {email = "different.person@example.com"},
]
maintainers = [
  {name = "Brett Cannon", email = "brett@example.com"}
]
description = "Lovely Spam! Wonderful Spam!"
readme = {file = "README.md", content-type = "text/markdown"}
license = "GPL-3.0-or-later"
# or
license = "MIT AND (Apache-2.0 OR BSD-2-Clause)"
license-files = ["LICEN[CS]E*", "vendored/licenses/*.txt", "AUTHORS.md"]
keywords = ["egg", "bacon", "sausage", "tomatoes", "Lobster Thermidor"]
classifiers = [
  # How mature is this project? Common values are
  #   3 - Alpha
  #   4 - Beta
  #   5 - Production/Stable
  "Development Status :: 4 - Beta",

  # Indicate who your project is intended for
  "Intended Audience :: Developers",
  "Topic :: Software Development :: Build Tools",

  # Specify the Python versions you support here.
  "Programming Language :: Python :: 3",
  "Programming Language :: Python :: 3.6",
  "Programming Language :: Python :: 3.7",
  "Programming Language :: Python :: 3.8",
  "Programming Language :: Python :: 3.9",
]

[project.optional-dependencies]
gui = ["PyQt5"]
cli = [
  "rich",
  "click",
]

[project.scripts]
spam-cli = "spam:main_cli"
# After installing, a `spam-cli` command will be available. The command will do the equivalent of `import sys; from spam import main_cli; sys.exit(main_cli())`.

[project.urls]
Homepage = "https://example.com"
Documentation = "https://readthedocs.org"
Repository = "https://github.com/me/spam.git"
Issues = "https://github.com/me/spam/issues"
Changelog = "https://github.com/me/spam/blob/master/CHANGELOG.md"
```

---
