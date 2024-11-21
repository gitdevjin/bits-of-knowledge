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


## Test Code Style That I prefer
```python
import pytest
from unittest.mock import mock_open, patch
from app.core.file_operations import read_toml, find_toml, get_config

def test_read_toml_good():
    toml_content = b"""
    fake_api_key = "abcdefg12345678"
    fake_model_name = "seneca-ai"
    """
    
    with patch("builtins.open", mock_open(read_data=toml_content)):
        result = read_toml("good.toml") 
        
        assert result == {'fake_api_key': 'abcdefg12345678', 'fake_model_name': 'seneca-ai'}


def test_read_toml_raises_ioerror():
    with patch("builtins.open", mock_open()) as mock_file:
        mock_file.side_effect = IOError("File not found")

        with pytest.raises(RuntimeError, match="Error reading TOML file"):
            read_toml("non_existent_file.toml")


def test_find_toml_found():
    with patch("os.path.expanduser", return_value="/mock/home/dir"):
        # Mock the list of files in the directory to contain the expected TOML file
        with patch("os.listdir", return_value=["addcom_config.toml", "other_file.txt"]):
            result = find_toml()
            # Check that the correct path is returned
            assert result == "/mock/home/dir/addcom_config.toml"


def test_find_toml_not_found():
    with patch("os.path.expanduser", return_value="/mock/home/dir"):
        with patch("os.listdir", return_value=["other_file.txt", "another_file.txt"]):
            result = find_toml()
            assert result is None


def test_get_config_with_valid_toml():
    # Mock the `find_toml` function to return a valid file path
    with patch("app.core.file_operations.find_toml", return_value="valid_toml_path.toml"):
        # Mock the `read_toml` function to return mock config data
        mock_toml_data = {"database": {"user": "admin", "password": "secret"}}
        with patch("app.core.file_operations.read_toml", return_value=mock_toml_data):
            # Call the function under test
            result = get_config()

            # Assert that the result matches the expected mock config
            assert result == mock_toml_data

# Test case 2: Test when TOML file is not found
def test_get_config_with_no_toml():
    # Mock the `find_toml` function to return None (file not found)
    with patch("app.core.file_operations.find_toml", return_value=None):
        # Call the function under test
        result = get_config()

        # Assert that the result is None when no TOML file is found
        assert result is None

```


Yes, that's correct! The side_effect property of a mock allows you to define a sequence of return values or behaviors for successive calls to the mocked function. Each time the mocked function is called, it will return the next value in the side_effect list.

In your case:

The first element of side_effect corresponds to the first call to toml.load, which handles the main config file (~/.codemage-config.toml).
The second element corresponds to the second call to toml.load, which processes the pyproject.toml file.
If there is a third call to toml.load, you would indeed need to add a third element to the side_effect list.

Example with Three Calls to toml.load
Let's say you also load a third TOML file, perhaps settings.toml. You'd structure your test like this:

python
Copy code
@patch("os.path.exists")
@patch("toml.load")
@patch("builtins.open", new_callable=mock_open)
def test_load_config_with_three_toml_files(self, mock_file, mock_toml_load, mock_os_exists):
    # Simulate file existence
    mock_os_exists.return_value = True

    # Simulate the contents of three files
    mock_toml_load.side_effect = [
        {"language": "Python", "OPENROUTER_API_KEY": "mock_openrouter_api_key"},  # ~/.codemage-config.toml
        {"tool": {"poetry": {"version": "0.9.0"}}},  # pyproject.toml
        {"theme": "dark", "notifications": True},  # settings.toml
    ]

    # Call the function under test
    config = load_config()

    # Assertions for the calls and combined config
    mock_file.assert_any_call("pyproject.toml", "r")
    mock_file.assert_any_call("settings.toml", "r")
    assert config == {
        "language": "Python",
        "OPENROUTER_API_KEY": "mock_openrouter_api_key",
        "version": "0.9.0",
        "theme": "dark",
        "notifications": True,
    }
Key Details:
side_effect Array: Each element in the array is returned in order with successive calls to toml.load. If you have a third call, you need to include a third object in side_effect.

Order Matters: The side_effect list determines the sequence of return values. If your code calls toml.load more times than there are elements in side_effect, it will raise an error (StopIteration).

Ensure File Mocking Matches: The mock_file must also simulate the files (pyproject.toml, settings.toml, etc.) being opened in the correct order. Use mock_file.assert_any_call() to verify each file was opened.