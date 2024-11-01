

# Ruff Formatter

## Install Ruff

Include the following codes:
```javascript
// pyproject.toml file
[tool.poetry.group.dev.dependencies]
ruff = "^0.7.1"
```

Run the following command:
```bash
poetry install
```


## Formatter Configuration
In the `pyproject.toml` file, configure the Ruff formatter as the following example:
```sql
[tool.ruff]
line-length = 100
exclude = [
    "tests/*",          # Ignore all files in the tests directory
    "docs/*",           # Ignore all files in the docs directory
    "*.pyc",            # Ignore all compiled Python files
    "migrations/*",     # Ignore all files in the migrations directory
    "example/*",
    "__pycache__/*",
    "*.env",
    "**/*.md",
]

[tool.ruff.format]
quote-style = "double"
indent-style = "tab"
docstring-code-format = true
docstring-code-line-length = 20
```

`docstring-code-format = true` : This will format code that starts with >>> inside `""" ... """ `

For example, If `docstring-code-format = true` option is applied to the following code,

```python
def calculate_square(n):
    """
    Calculate the square of a number.

    Example:
        >>> result = calculate_square(5)
        >>> print(result)  # Outputs 25
        >>> long_calculation = calculate_square(10) + calculate_square(20) + calculate_square(30)
        >>> print(long_calculation)  # Outputs 1300
    """
    return n ** 2
```

It will become formatted as follows:
```python
def calculate_square(n):
    """
    Calculate the square of a number.

    Example:
        >>> result = calculate_square(5)
        >>> print(result)  # Outputs 25
        >>> long_calculation = (
        ...     calculate_square(10)
        ...     + calculate_square(20)
        ...     + calculate_square(30)
        ... )
        >>> print(long_calculation)  # Outputs 1300
    """
    return n ** 2
```
Docstring code can be executed. Refer to [doctest Docs](https://docs.python.org/3/library/doctest.html)

## Run the `Ruff formatter`

```bash
poetry run ruff format .
```

# Ruff Linter

## Linter Configuration
Since I am using ruff formatter together, I only need a bit more configuration settings.
```sql
[tool.ruff.lint]
select = ["E4", "E7", "E9", "F", "F841"]
```

### I asekd ChatGPT what those are for.
`E4`: This code typically corresponds to various syntax errors or issues related to the structure of your code. It may include checks for problems such as unclosed parentheses, braces, or other syntax-related issues.

`E7`: This code is generally related to type errors or issues involving type annotations. It may indicate that there are inconsistencies or issues with type hints in your code, which can lead to confusion or runtime errors.

`E9`: This error code often relates to import errors or issues with the organization of your import statements. It can flag problems such as unused imports or circular imports that can complicate module loading.

`F`: This category usually encompasses formatting issues. It can include a variety of stylistic checks, such as line length, whitespace usage, and general adherence to PEP 8 (the Python style guide).

`F841`: This code specifically checks for assigned variables that are never used.

### We could also do:
```sql
[tool.ruff.lint.per-file-ignores]
"__init__.py" = ["E402"]
"**/{tests,docs,tools}/*" = ["E402"]
```

### I asked ChatGPT what those configurations are for:

ChatGPT => The `E402` error, which means "module level import not at top of file," occurs when import statements are not placed at the top of a Python file. According to PEP 8, the Python style guide, all imports should be at the top of the file, right after any module comments and docstrings but before any other code.

Here are common scenarios where E402 might happen:

### 1. Conditional Imports:
```python
if some_condition:
    import os  # E402 error: Import is not at the top level of the file
```

### 2. Delayed Imports for Performance Reasons:

Sometimes, you might place an import further down in the file to avoid unnecessary imports until they are actually needed, such as in a function:
```python
def some_function():
    import numpy as np  # E402 error: Import is inside a function
    return np.array([1, 2, 3])
```
In those scenarios, you can use the configuration for the files that need to ingore the rules.


## How to skip linting some pieces of code in a file

### 1. Ignore Specific Lines
You can add an `# noqa` comment at the end of a line to ignore linting on that specific line. To ignore specific rules, specify the rule codes:

```python
if self.model is "groq":  # noqa: F632
```
In this example, Ruff will ignore the `F632` warning on that line.

### 2. Ignore a Block of Code
To ignore a block of code, wrap the code with `# noqa: BEGIN` and `# noqa: END`:

```python
# noqa: BEGIN
# This code block will be ignored by the linter
if some_condition:
    import sys  # Normally flagged as E402
# noqa: END
```

### 3. Ignore Rules for the Whole File
To ignore specific rules for the entire file, add a comment at the top of the file listing the rule codes to ignore:

```python
# ruff: noqa: E402, F632
```

This approach is useful if the whole file has patterns that would otherwise be flagged by Ruff, such as non-top-level imports or value comparisons using is

## Run the `Ruff Linter`
```bash
poetry run ruff check .
```