# markdown-it-py`

## What is *markdown-it-py*

* *markdown-it-py* is a Python implementation of the parsing engine originally from *markdown-it* (JavaScript).
* It aims to provide a **fast**, **CommonMark-compliant** Markdown parser for Python, with support for configurable syntax and a plugin architecture.
* It is designed for flexibility: you can extend or override parsing/rendering rules, add plugins, and adapt Markdown parsing to different needs (e.g. tables, footnotes, front-matter, etc.)

Common use cases: static site generators, documentation pipelines, content processing, automated conversion of Markdown → HTML (or other formats), embedding custom syntax, analysis of Markdown content.

---

## Installation

Typical installation via `pip`:

```bash
pip install markdown-it-py[plugins]
```

Optionally with extras (e.g. linkify, plugins):

```bash
pip install markdown-it-py[linkify,plugins]
```

Alternatively, via Conda (if using conda environment):  

```bash
conda install -c conda-forge markdown-it-py
```

(or with extras)

After installation, you can import and use it in Python code.

---

## Basic Usage; Quick Start

Here’s a minimal example to convert a Markdown string into HTML:

```python
from markdown_it import MarkdownIt

md = MarkdownIt()
html = md.render("This is **bold** and this is *italic*.")
print(html)
```

This will output something like:

```html
<p>This is <strong>bold</strong> and this is <em>italic</em>.</p>\n
```

If you want introspection (see how the parser breaks down the Markdown into tokens) you can do:

```python
from markdown_it import MarkdownIt
from pprint import pprint

md = MarkdownIt()
tokens = md.parse("Hello *world*")
pprint(tokens)
```

Each token describes a syntactic element (paragraph, inline content, emphasis, etc.), and you can inspect token type, nesting, content, children, etc.

Thus *markdown-it-py* works in two stages:

1. **Parsing**: raw markdown → stream of tokens (or optionally a syntax tree)
2. **Rendering**: tokens → output (HTML by default, but could be other formats via custom renderers)

---

## Parser Configuration and Presets

When instantiating the parser, you can choose a preset or provide custom options. The presets control which syntax rules and features are enabled.

Some of the presets:

* `"commonmark"`; the default. Implements the core specification strictly.
* `"zero"`; minimal parsing: basically paragraphs and plain text only. Useful for building a custom Markdown dialect from scratch.
* `"js-default"`; corresponds approximately to the original JavaScript markdown-it default (with extra features like tables/strikethrough depending on configuration).
* `"gfm-like"`; approximates GitHub-Flavored Markdown: enables features like tables, strikethrough, autolinking (linkify).

You can also override parsing/rendering behavior by supplying an options dict, for example:

```python
md = MarkdownIt("commonmark", { "html": True, "breaks": True })
```

Customising options allows enabling/disabling parsing of HTML inside Markdown, customizing quote styles, line-break behavior, etc.

---

## Extending with Plugins and Custom Renderers

One of the major strengths of *markdown-it-py* is its extensibility.

### Plugins

There are a number of available plugins (via the companion package mdit-py-plugins), enabling extra syntax features beyond vanilla Markdown/CommonMark. For example:

* front-matter support
* footnotes
* definition lists, task lists, containers, math (LaTeX), heading anchors, word-count utilities, etc.

Example usage:

```python
from markdown_it import MarkdownIt
from mdit_py_plugins.front_matter import front_matter_plugin
from mdit_py_plugins.footnote import footnote_plugin

md = MarkdownIt("commonmark") \
       .use(front_matter_plugin) \
       .use(footnote_plugin) \
       .enable("table")

html = md.render(my_markdown_text)
```

### Custom Renderers / Render Rules

If you want to control how the parsed tokens are output (e.g. generate custom HTML, or output to a different format), you can define custom render rules or provide your own renderer class.

For example, overriding how emphasis is rendered:

```python
def custom_em_open(self, tokens, idx, options, env):
    return '<em class="myclass">'

md.add_render_rule("em_open", custom_em_open)
```

Alternatively subclass the default renderer:

```python
from markdown_it.renderer import RendererHTML

class MyRenderer(RendererHTML):
    def em_open(self, tokens, idx, options, env):
        return '<em class="myclass">'

md = MarkdownIt("commonmark", renderer_cls=MyRenderer)
output = md.render("*text*")
```

This allows full control over output formatting without changing the parsing logic.

---

## Syntax-Tree Representation (AST) and Token Stream Inspection

Beyond rendering HTML, *markdown-it-py* lets you work at the token level, or even build a syntax tree.

* The parser produces a **flat token stream**. Each token includes metadata: type, tag, nesting, children (for inline), content, etc.
* If you prefer a hierarchical tree structure (AST-like), you can convert the token stream into a **syntax tree** using SyntaxTreeNode. Once converted, you can traverse, inspect, and manipulate document structure programmatically.

This is useful for static analysis, transformations, custom rendering workflows, or building documentation tools / custom Markdown preprocessors.

---

## Use Cases

Because of its design, *markdown-it-py* is well suited for:

* Static site generation pipelines; converting Markdown content into HTML (or other formats) reliably.
* Documentation generation; content management systems; blog engines.
* Situations requiring extensible syntax (footnotes, front-matter, tables, task lists, math, custom syntax) that go beyond core Markdown.
* Automated batch processing: e.g. converting many Markdown documents to HTML in CI/CD, or sanitizing/rendering Markdown content in backend services.
* Programs that need access to structured representation (AST / tokens) for analysis, rewriting, transformations, or custom rendering output.

---

## Minimal Example

```python
from markdown_it import MarkdownIt
from mdit_py_plugins.footnote import footnote_plugin

md = ( MarkdownIt("gfm-like", { "html": True, "breaks": True })
       .use(footnote_plugin) )

md_text = """
# Example

Some **bold text**, a [link](https://example.com), and a footnote.[^1]

[^1]: This is the footnote content.
"""

html = md.render(md_text)
print(html)
```

This code:

* Uses a GitHub-Flavored Markdown-like preset
* Allows HTML inside Markdown and converts line breaks to `<br>` (or equivalent)
* Enables footnote syntax via plugin
* Outputs HTML that respects bold, links, footnotes, headings, etc.

That demonstrates the flexibility and real-world applicability of *markdown-it-py*.

---
