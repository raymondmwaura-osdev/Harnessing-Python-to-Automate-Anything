## `content`

* The `content` attribute in `markdown_it.token.Token` stores the textual data associated with the token. For block-level tokens like paragraphs or headings, it contains the plain Markdown content inside the block. For inline tokens, it may store the literal text, such as the characters inside emphasis, code spans, or links. This attribute is central for rendering, transformations, and any processing that relies on the raw text of the token.
* **Type**: `str`
* **Mutability**: Mutable; the content can be updated after token creation, allowing modification of the token’s text before rendering or analysis.
* **Default value**: An empty string `''` at initialization, until the parser populates it with the actual token content.

### Example Usage

```python
from markdown_it import MarkdownIt

md = MarkdownIt()
tokens = md.parse("This is **bold** text.")

# Accessing content of the first token
print(tokens[0].content)  # Output: 'This is **bold** text.'

# Modifying content (advanced usage)
tokens[0].content = "Modified text"
print(tokens[0].content)  # Output: 'Modified text'
````

---
