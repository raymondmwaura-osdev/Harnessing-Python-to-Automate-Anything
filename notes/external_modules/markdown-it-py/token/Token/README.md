# `markdown_it.token.Token`

## Overview

* The `markdown-it-py` library parses Markdown into a **token stream** rather than a full abstract syntax tree (AST).
* Each element in this stream is a `Token` instance, representing a structural or inline element from the Markdown source (e.g., paragraphs, headings, text, emphasis, links, code blocks).
* The token stream is subsequently used for rendering (e.g., to HTML) or for further transformations by plugins or custom renderers.

In essence, `Token` is the fundamental unit of the parsed Markdown representation in `markdown-it-py`.

---

## Key Attributes of `Token`

* **`type` (str)**: The kind of token, such as `"paragraph_open"`, `"text"`, `"em_open"`, or `"link_close"`.
* **`tag` (str)**: Corresponding HTML tag name, e.g., `"p"`, `"em"`, `"strong"`.
* **`nesting` (int: -1, 0, 1)**: Indicates the token’s role in a nested structure: `1` = opening tag, `-1` = closing tag, `0` = self-closing or standalone.
* **`level` (int)**: Nesting depth within the document parse state.
* **`attrs` (dict or list)**: Attributes for the HTML tag, such as `href` or `title` for links. May be empty.
* **`children` (list of `Token` or None)**: Nested tokens inside container tokens (commonly used for inline elements).
* **`content` (str)**: Literal content carried by the token (e.g., text or code block content).
* **`markup` (str)**: Original Markdown markup that generated the token (e.g., `"*"`, "\`", fence markers).
* **`info` (str)**: Extra information, such as language for fenced code blocks.
* **`block` (bool)**: Indicates whether the token is block-level or inline-level.
* **`hidden` (bool)**: If `True`, the token is skipped during rendering (used in features like tight lists).
* **`map` (list[int, int] or None)**: Line numbers in the original Markdown corresponding to this token. Useful for diagnostics or source mapping.
* **`meta` (dict)**: Arbitrary user or plugin data that can be attached to the token for custom functionality.

---

## Example: Parsing Markdown and Inspecting Tokens

```python
from markdown_it import MarkdownIt

md = MarkdownIt()
tokens = md.parse("Hello *world*")

for token in tokens:
    print(token)
```

This produces a stream of tokens such as:

* `Token(type='paragraph_open', tag='p', nesting=1, ...)`
* `Token(type='inline', tag='', nesting=0, children=[ ... inline tokens ... ])`
* `Token(type='paragraph_close', tag='p', nesting=-1, ...)`

Inline tokens (found in the `children` of an `'inline'` token) represent text segments, emphasis markers, etc.

It is also possible to manually create a `Token`:

```python
from markdown_it.token import Token

t = Token("paragraph_open", "p", 1, block=True, map=[1, 2])
```

---

## Serialization and Interoperability

* `Token.as_dict()` converts a token (and optionally its children) into a Python dictionary suitable for JSON serialization, storage, or transmission.
* `Token.from_dict()` reconstructs a token from such a dictionary, enabling round-trip transformations or external tooling to consume the token stream.

This is particularly useful for plugins, analysis tools, or alternate renderers such as JSON or AST-like structures.

---

## Design Philosophy

* `markdown-it-py` uses a **linear token stream** rather than a strict AST for simplicity and performance.
* Hierarchy and structure are maintained via `nesting`, `level`, and `children`, while the underlying model remains sequential and flat.
* This design is well-suited for Markdown, where many constructs open and later close, and the separation between inline and block elements is important.

---

## Use Cases for Direct `Token` Manipulation

* Writing plugins to modify the parsed output before rendering.
* Creating custom renderers (e.g., JSON, XML, or non-HTML formats).
* Performing static analysis, linting, or programmatic transformations on Markdown.
* Integrating Markdown parsing into applications requiring programmatic access to document structure and metadata.

---
