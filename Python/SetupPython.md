# Python

## Installation

Following this: https://stackoverflow.com/questions/41427500/creating-a-virtualenv-with-preinstalled-packages-as-in-requirements-txt
and this: https://packaging.python.org/en/latest/tutorials/installing-packages/

  - Install python (https://www.python.org/downloads/)
    - Set `py` allias: `Set-Alias 'py' 'C:\Local\Python3_12\python.exe'`
	  + This can be done in powershell profile at `$HOME\Documents\PowerShell\Microsoft.PowerShell_profile.ps1`
	  + More details can be found at: https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_profiles?view=powershell-7.4
    - For new instalation, ensure pip, setuptools, and wheel are up to date: `py -m pip install --upgrade pip setuptools wheel`
  - Install virtual environment: `py -m pip install virtualenv`
  - Create virtual env.: `py -m virtualenv venv`
  - Activate virtual env.: `venv/Scripts/activate.ps1`
    + on Linux/WSL: `source venv/Scripts/activate`
  
### Setup python environment

The quickest way is when you have a `requirements.txt` file:
  - To install the requirements in the current virutal env: `pip install -r requirements.txt`
    - The **requirements.txt** file is created by `python -m pip freeze > requirements.txt`
    - Notebook then can be started with: `jupyter notebook`
  - Can have multiple requirements file, that reference each other: Dev -> Test -> Prod
    ```
    # Prod: requirements-prod.txt

    # Test: requirements-test.txt
    -r requirements-prod.txt

    # Dev: requirements-dev.txt
    -r requirements-test.txt
    ```

### Secrets with version control and Dot-env
- Store local secrets: API key, password, etc
- Should be ignored by version control
- To use, need `python-dotenv` package
  ```
  from dotenv import load_dotenv

  # Loading
  load_dotenv()

  # Usage
  os.getenv('API_KEY')
  ```
  
#### Jupyter notebook setup from scratch

This assume the virtual environment has been created and actived
  - Install Jupyter notebook `pip install notebook`
  - To add current active environment to jupyter as "**DN4**" (see: https://anbasile.github.io/posts/2017-06-25-jupyter-venv/)
    ```shell
    # Should already installed
    # pip install ipykernel
	
    ipython kernel install --user --name=DN4
	```
    + to remove an installed kernel: 
	```
	# get the list of kernel
	jupyter kernelspec list
	
	# removed the <unwanted-kernel>
	jupyter kernelspec uninstall <unwanted-kernel>
	```
  - To generate the configuration file 
    ```shell
    # The configuration file will be "C:\Users\<UserName>\.jupyter\jupyter_server_config.py"
    jupyter server --generate-config
    # If received PowerShell response Jupyter command 'jupyter-server' not found
    # can try to install Anaconda Powershell and execute the jupyter server command again, which usually will work.
	```
  - To make jupyter start at the git checkout
    + Edit the "jupyter_server_config" file to set c.ServerApp.root_dir as sample below
	  ```text
      c.ServerApp.root_dir = 'c:/git/wtg'
	  ```
	+ Start the jupyter notebook from command line (not windows link): `jupyter notebook`

### PIP

Download package from PyPI.org (Python Package Index) by default
  - Install: `python -m pip install arrow` --> good practice in case `pip` is mapped to something else
    + Specify version: `python -m pip install arrow=1.0.0` === `python -m pip install "arrow = 1.0.0"`
    + Upgrade existing: `python -m pip install --upgrade arrow` === `python -m pip install -U arrow`
    + Constraint version: `python -m pip install "arrow > 1.0.0, < 3.0"`
    + Install from repos: `python -m pip install "demo_pkg @ git+<GITHUB_URL>"` --> more details from "VCS support" in pip documentation
    + Install from file: `python -m pip install <FILE_PATH>` --> process the "**pyproject.toml**", build a "wheels" file, then install 
  - Uninstall: `python -m pip uninstall arrow` --> not uninstall dependency
  - List package: `python -m pip list`
  - Show dependency: `python -m pip show anyio` --> show it "Required-by", as well as its own "Requires"

In Dev, we can do "editable" installs: not creating wheel file, but add the project file into search path --> changes in file will reflect immediately in running environment
  - Example: `pip install -e ~/projects/demo_pkg`

#### Alternative to PIP
  1. UV: written in Rust, very fast and can completely replace PIP, Poetry, Pipenv
    - Main URL: https://docs.astral.sh/uv
    ```
    # installation
    pip install uv

    # init. new project
    uv init uv-demo

    # add dependency (first, need to get into the project dir)
    # will also add virtual env
    cd uv-demo
    uv add requests

    # run pip to list installed packages in virtual env
    uv add pip
    uv run pip list
    ```
    - uv create a uv.lock with details about the virtual environment, so it could be recreated exactly very fast when needed
  2. Poetry: Focus on manage project dependencies, with friendly UI
  3. Pipenv: also more about project dependencies

## Tools

### Pylint
  - Popular tools for code style checking
  - Can be installed with pip

**Example**:
```
# run on all file in folder ./server
pylint ./server

# Customize Pylint
# 1. generate configuration file
pylint --generate-rcfile > .pylintrc

# 2. Run Pylint with custome configuration
pylint --rcfile=.pylintrc example.py
```

### Ruff
  - Similar to Pylint, written in Rust --> faster, focus on common linting & formatting, could fix many violation
    + Shallower static analysis, no codesmell
  - Configuration file: `ruff.toml`, or `pyproject.toml`
  - Similar to `Black` in ability to automatically format code

**Example**:
```
# Check the current directory with ".", or "path/to/code"
ruff check .

# Fix automatically
ruff check --fix .

# Format code
ruff format .
```

### pre-commit framework
  - to manage & run Git hooks in Python project --> automates code quality checks & formatting befor code is committed

### linter in VS Code
  - Can install extensions for Pylint, Ruff, etc.
  - Can then run these tools from the IDE (i.e. With Ruff: right click -> format code)

## Documentation
  - Using **docstring** to document code: first string of module/function
    + Normally surround by 3 double-quote:`"""some description"""`
  - Generate documentation from doc-string --> Using "**Sphinx**"
    + Sphinx use `reStructuredText` as input