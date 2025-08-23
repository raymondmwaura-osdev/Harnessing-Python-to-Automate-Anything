# `name`

Specifies the argument’s identifier; either a positional name (e.g., `'filename'`) or optional flags (e.g., `'-f', '--foo'`).

## Example

```python
import argparse

parser = argparse.ArgumentParser()
parser.add_argument('filename')
args = parser.parse_args()
print(args)
```

### Output and Explanation

```bash
$ python script.py document.txt
Namespace(filename='document.txt')
```

+ `Namespace(...)` is what `argparse` returns after parsing.
+ It shows the argument name (`filename`) and the value supplied (`'document.txt'`).

```bash
$ python script.py
usage: script.py [-h] filename
script.py: error: the following arguments are required: filename
```

+ Since no value was provided, `argparse` prints:
    
    1. **Usage message**: Shows required format(`script.py [-h] filename`).
    2. **Error message**: Explicitly states which argument is missing (`filename`).
