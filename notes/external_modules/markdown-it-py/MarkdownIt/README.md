# `MarkdownIt`

## Overview

The `MarkdownIt` class is the central orchestration layer of the **markdown-it-py** ecosystem. It provides a configurable, plugin-driven pipeline for transforming Markdown input into tokens and ultimately rendered output. Think of it as a modular parsing engine engineered for extensibility, determinism, and standards alignment.

---

## Key Responsibilities

`MarkdownIt` owns three primary operational domains:

1. **Configuration Management**
   Initializes presets, toggles feature flags, and sets parser behavior (e.g., HTML passthrough, softbreak handling, typographic options).

2. **Tokenization Pipeline**
   Converts raw Markdown text into a structured sequence of tokens. This standardized representation supports downstream transformations, analytics, and custom rendering logic.

3. **Rendering Pipeline**
   Converts token streams into output formats; HTML by default, but fully customizable via renderer overrides.

---

## Instantiating `MarkdownIt`

The class can be constructed with either a preset name or a zero-configuration minimal baseline.

```python
from markdown_it import MarkdownIt

md = MarkdownIt()  # defaults to CommonMark
```

Using a preset:

```python
md = MarkdownIt("gfm-like") # Github flavored markdown.
```

Applying option flags:

```python
md = MarkdownIt("commonmark", {
    "html": True,     # allow raw HTML
    "breaks": True,   # convert newlines to <br>
})
```

This pattern enables a governance-style approach: select a highly constrained baseline, then incrementally enable capabilities.

---

## Core Methods

### `render(text, env=None)`

Executes the full parse-render lifecycle.

```python
html = md.render("# Header\nSome *text*.")
```

* Produces HTML output.
* `env` acts as a shared metadata carrier during parsing and rendering.

---

### `parse(text, env=None)`

Returns the internal token stream without rendering.

```python
tokens = md.parse("Hello *world*")
```

This is essential for workloads requiring structural analytics, custom transformations, or downstream non-HTML rendering.

---

### `use(plugin, *params)`

Injects plugin modules into the parsing pipeline.

```python
from mdit_py_plugins.footnote import footnote_plugin

md = MarkdownIt().use(footnote_plugin)
```

Plugins can register custom rules, modify existing rules, introduce new token types, or override renderer logic. This is the extensibility vector for building domain-specific Markdown dialects.

---

### `enable(rules)` / `disable(rules)`

Activates or deactivates rule groups within the parser.

```python
md.enable(["table"])
md.disable(["html_inline"])
```

This facilitates operational control of syntax exposure—for example, enabling GFM-style tables only where mandated.

---

## Render Customization

### Adding Rule Overrides

You can override rendering rules granularly.

```python
def custom_strong_open(self, tokens, idx, options, env):
    return '<strong class="accent">'

md.add_render_rule("strong_open", custom_strong_open)
```

### Supplying a Custom Renderer Class

Architectural alternative for end-to-end custom output formats:

```python
from markdown_it.renderer import RendererHTML

class MyRenderer(RendererHTML):
    def em_open(self, t, i, o, e):
        return "<em data-style='emphasis'>"

md = MarkdownIt(renderer_cls=MyRenderer)
```

---

## Lifecycle Architecture

1. **Preprocessing**
   Normalization and preparation of raw text.

2. **Block Parsing**
   High-level structures: headings, lists, code blocks, blockquotes.

3. **Inline Parsing**
   Fine-grained content: emphasis, links, HTML spans, code spans.

4. **Token Stream Emission**
   Nested stream of structural tokens.

5. **Rendering**
   Token-driven output generation.

This architecture ensures deterministic parsing, modular rule composition, and high performance.

---

## Minimal End-to-End Example

```python
from markdown_it import MarkdownIt

md = MarkdownIt("gfm-like", { "breaks": True })

sample = """
# Title

Some **bold** text, a link: https://example.com, and a list:

- Alpha
- Beta
"""

html = md.render(sample)
print(html)
```

This showcases the default operational flow: configuration → parsing → rendering.

---
