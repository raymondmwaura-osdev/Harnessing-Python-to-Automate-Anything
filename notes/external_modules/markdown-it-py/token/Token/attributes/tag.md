## `tag`

* The `tag` attribute in `markdown_it.token.Token` represents the HTML tag associated with the token when rendering Markdown to HTML. It indicates the semantic element corresponding to the token, such as `<p>` for paragraphs, `<h1>` for top-level headings, `<li>` for list items, or `<code>` for inline code. This attribute is essential for HTML generation and enables precise mapping between Markdown syntax and the resulting DOM structure.
* **Type**: `str`
* **Mutability**: Mutable; the attribute can be modified after token creation, which is occasionally done when customizing rendering behavior or implementing plugins that alter output tags.
* **Default value**: An empty string `''` initially, until the parser assigns a specific HTML tag based on the token type.

### Example Usage

```python
from markdown_it import MarkdownIt

md = MarkdownIt()
tokens = md.parse("# Heading 1\n\nThis is a paragraph.")

# Accessing the tag of the first token
print(tokens[0].tag)  # Output: 'h1'

# Modifying the tag (advanced usage)
tokens[0].tag = 'h2'
print(tokens[0].tag)  # Output: 'h2'
````

---
