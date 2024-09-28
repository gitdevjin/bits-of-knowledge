## Step-by-Step Guide to Start a Poetry Project

### 1. Install Poetry (if you haven't already): If you haven't installed Poetry yet, you can do so using the following command:

```bash
curl -sSL https://install.python-poetry.org | python3 -
```

### 2. Create a New Directory (optional): Navigate to the directory where you want to create your project and create a new folder for it. For example:

```bash
mkdir my-poetry-project
cd my-poetry-project
```
### 3. Initialize a New Poetry Project: Use the following command to create a new Poetry project:

```bash
poetry init
```
This will prompt you to enter some details about your project, such as the project name, version, description, author, etc. If you want to skip the interactive prompt and use default values, you can use:

```bash
poetry new my-poetry-project
```

### 4. Install Dependencies: After initializing your project, you can add dependencies using:

```bash
poetry add package-name
```
Replace `package-name` with the name of the library you want to install (e.g., requests, flask, etc.).

### 5. Activate the Virtual Environment (optional): If you want to activate the virtual environment created by Poetry, you can run:

```bash
poetry shell
```
This will drop you into a new shell where the virtual environment is activated.

### 6. Run Commands: You can run Python scripts or commands using:

```bash
poetry run python script.py
```
Replace script.py with the name of your Python script.

If you have setted a script-running command in pyproject.toml file, you can run it as follows:
```bash
poetry run codemage
```