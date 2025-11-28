## `type`

* The `type` attribute in `markdown_it.token.Token` serves as an identifier for the kind of token represented within the Markdown parsing process. It distinguishes among different structural or inline elements, such as headings, paragraphs, list items, or code blocks. This attribute is critical for downstream processing, allowing renderers or plugins to determine appropriate handling for each token type.
* **Type**: `str`
* **Mutability**: Mutable; the attribute can be reassigned after token creation, although this is rarely necessary outside of advanced parsing or transformation logic.
* **Default value**: An empty string `''` upon initialization, until the token type is explicitly set by the parser.

### Example Usage

```python
from markdown_it import MarkdownIt

md = MarkdownIt()
tokens = md.parse("# Heading 1\n\nThis is a paragraph.")

# Accessing the type of the first token
print(tokens[0].type)  # Output: 'heading_open'

# Modifying the type (advanced usage)
tokens[0].type = 'custom_heading'
print(tokens[0].type)  # Output: 'custom_heading'
````

---
