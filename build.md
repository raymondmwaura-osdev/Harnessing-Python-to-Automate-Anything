# Build a Wheel

```sh
pip install build
```

Write `pyproject.toml` at the project's root directory then run this command:

```sh
python -m build
```

This creates a `dist/` directory with two files:

+ `your_package-0.1.0-py3-none-any.whl` (the wheel)
+ `your_package-0.1.0.tar.gz` (source distribution)


### Test the Wheel

```sh
# Create a virtual environment.
virtualenv test_venv
source test_venv/bin/activate

# Install the wheel.
pip install dist/your_package-0.1.0-py3-none-any.whl

# Test the command set in `pyproject.toml`.
command --help
command --version
command
```
