# Testing `Poetry` project with `Pytest`

## install `pytest`
```bash
poetry add --dev pytest
```

## Running `pytest`

```bash
poetry run pytest
```

This command will run files named `test_*.py` or `*_test.py`

For more detailed test result, you can use `-v` option

```bash
poetry run pytest -v
```

for more detailed test information
```bash
poetry run pytest -vv
```

## How to mock file system with `pytest`

```python
import os
from unittest.mock import patch
from code_mage.loadConfig import load_config  # Adjust the import path accordingly


class TestLoadConfig:
	@patch("os.path.exists")
	@patch("toml.load")
	def test_load_config_file_exists(self, mock_toml_load, mock_os_exists):
		# Simulate that the file exists
		mock_os_exists.return_value = True

		# Simulate the contents of the file
		mock_toml_load.return_value = {
			"language": "Python",
			"OPENROUTER_API_KEY": "mock_openrouter_api_key",
		}

		# Call load_config function
		config = load_config()

		# Assertions
		mock_os_exists.assert_called_once_with(os.path.expanduser("~/.codemage-config.toml"))
		mock_toml_load.assert_called_once_with(os.path.expanduser("~/.codemage-config.toml"))
		assert config == {"language": "Python", "OPENROUTER_API_KEY": "mock_openrouter_api_key"}

	@patch("os.path.exists")
	def test_load_config_file_not_exists(self, mock_os_exists):
		# Setup mock behavior
		mock_os_exists.return_value = False  # Simulate that the file does not exist

		# Call the function under test
		config = load_config()

		# Assertions
		mock_os_exists.assert_called_once_with(os.path.expanduser("~/.codemage-config.toml"))
		assert config == {}
```

### Here's the breakdown:
- Mocking os.path.exists() with mock_os_exists:

When you call load_config(), the os.path.exists() function is used to check whether the file exists at the given path.
Normally, os.path.exists() would check the actual file system, but in the test, you are mocking this function using mock_os_exists.
By doing so, you're replacing the real os.path.exists() with your mock object. This allows you to control what it returns and to check if it was called with the correct arguments.
So, when load_config() calls os.path.exists(), it will call the mock object (mock_os_exists) instead of the real one. The mock object "remembers" the arguments it was called with, which is why you can assert if it was called with the expected path:
python
Copy code
mock_os_exists.assert_called_once_with(os.path.expanduser("~/.config/codemage/config.toml"))
Mocking toml.load() with mock_toml_load:

The load_config() function uses toml.load() to read the contents of the file if it exists.
Normally, toml.load() would try to load the actual file contents, but in the test, you're mocking this function using mock_toml_load.
By mocking toml.load(), you can control what data is returned without needing an actual file. For example, in the test, you're mocking it to return a specific dictionary like {"language": "Python", "version": "3.10"}.
So when load_config() calls toml.load(), it will call the mock instead of the real one, and you can verify that it was called with the correct path:
python
Copy code
mock_toml_load.assert_called_once_with(os.path.expanduser("~/.config/codemage/config.toml"))
To summarize:
mock_os_exists: Mocks os.path.exists(), intercepting the call to check whether the file exists at a particular path. It "remembers" the path it's called with, allowing you to check if it was called with the correct path.
mock_toml_load: Mocks toml.load(), allowing you to simulate loading a configuration file without actually reading from the file system. You can control the returned content of the "file" and verify that it was called with the correct path.
Correct test behavior:
os.path.exists() is mocked to simulate whether the file exists, and you check that it was called with the correct path.
toml.load() is mocked to simulate reading the file contents, and you check that it was called with the correct path.