## How to publish `poetry` porject
#### 1. Create PyPI account and generate API_KEY

#### 2. Set up the API_KEY in `config.toml` file for poetry.
Poetry `config.toml` for mac is located in `~/Library/Application Support/pypoetry`

```toml
[pypi]
username = "__token__"
password = "pypi-<your-token>"
```
you may need to set up the key as following:

```bash
poetry config pypi-token.pypi <your-token>
```

#### 3. Build your poetry project in the root dir of your project
```bash
poetry build
```

#### 4. Run the following commend to publish it 
```bash
poetry publish
```
