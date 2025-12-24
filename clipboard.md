# Clipboard

Use the `pyperclip` library to access the system's clipboard in Python. It is cross-platform. There also exists `pyperclip3` which designed for python3.

```sh
pip install pyperclip
```

```python
import pyperclip

# Copy text to clipboard.
pypercip.copy("Text")

# Paste from clipboard.
text = pyperclip.paste()
```

**Note**: On Linux, `pyperclip` requires some packages to be installed. `xclip` or `xsel` for X11, and `wl-clipboard` for Wayland. These packages act as a bridge between `pyperclip` and the system's clipboard. Without them, `pyperclip` won't be able to access the system's clipboard.
