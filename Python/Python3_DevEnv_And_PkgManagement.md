# Development Environments and Package Management in Python 3

- Course URL: <https://app.pluralsight.com/ilx/video-courses/python-3-development-environments-package-management/course-overview>

## Managing Python Packages with Pip

### Pip basics

- By default: install packages from <pypi.org>
- Basic instruction: `pythom -m pip install <pkg-name>`
  - log will show version, and where the package has been installed
  - `pip uninstall <pkg-name>` --> remove only the package (not its dependencies)
  - `pip list` --> show everything that has been installed
  - `pip show <pkg-name>` --> show dependency information
- Requirement specifiers: pkg-name and version specifier
  - default: latest version
  - `<pkg-name> == XX.YY.ZZ`: specific version --> can also use "~=", "!=", ">", "<", etc., or even "> X.Y.Z, < A.B"
  - Can combine multiple version specifiers with comma --> meaning "AND" of specifiers
  - `install -U/--upgrade <pkg-name>`: upgrade to latest version
- Dependency requirements: specified in `setup.py` of the installed package
  - New way: `pyproject.toml` --> split into different dependencies types: doc, dev, all, etc.
    - Install specific group of dependency: `python -m install "fastapi[doc,dev]"`
- Full requirements specifiers documents: [pep-0508](https://peps.python.org/pep-0508/)
  - Other topic: equivalent, environment marker/restriction

### Packages not hosted on Pypi

- Can install packages from github --> for example: temporary bugs fixed that are not yet officially release
  - Syntax: `python -m pip install "<pkg-name> @ git+<pkg-git-url>"`
  - More details in pip documentation, under "VCS Support": git --> "git+", Mercurial --> "hg+", Subversion --> "svn+", Bazaar --> "bzr+"
  - Can even specify version, tag or commit
- For local filesystem: `python -m pip install <project-folder>` --> build to the *wheel* file, then install
  - Need to conforms to a specific directory structure, and have `pyproject.toml`
  - install a *wheel* file: `python -m pip install <path-to-wheel-file>`
- **Editable install**: often for local file install but without copying the file
  - Equivalent to adding the local file to search path, so changes in source file will take effect immediately.
  - Only need to re-install for change in project metadata (i.e. "pyproject.toml")
  - Syntax: `pip install -e <path-to-project>`

### Installing outside of a project

- Useful for command or utility --> available everywhere
  - Not install into a virtual environment, but on global version of python
  - Install for local user only: `pip install --user <pkg-name>` --> globally available for this user
  - Installed command/utilities won't work automatically unless their path is added to environment
- `pipx`: let user install & run "end-user application" in python
  - Should start with `pipx ensurepath` --> setup the path
  - Install each app in its own virtual environment, but the app still available in your shell

### Configuring pip

- To add extra options when using with proxy server, custom SSL certificates, etc.
  - Can also do it on commandline, but this save from repeated typing
  - Named different depending on environment: `pip.conf` vs `pip.ini`
- Use hosted repository: `pip install <pkg-name> --index-url <hosted-repository>` --> won't download from pypi
- Proxy settings: `--proxy scheme://[user:password@]proxy.server.port`
- Other settings: "trusted-host", "cert" and "client-cert"

Example:

```ini
[global]
timeout = 60
index-url = https://my-repo.example.com
```

## Virtual Environments & Project Dependencies

- Create new virtual environment: `python -m venv venv`
  - To activate, run the ". venv/bin/activate" script ("venv/Scripts/activate.ps1" on windows)
  - "Activate" sets up your shell so that
    - The Python interpreter and packages/dependencies installed in that environment are used instead of the globally installed ones
    - `sys.path` is modified to include the site-packages directory inside the virtual environment
  - To go back to global environment, run `deactivate`/`deactivate.ps1`
- Specify the required packages for an environment: `pip freeze > requirements.txt`
- Requirement file: use the same syntax as pip's requirement specifier
  - Can have multiple requirement for different environment: prod, test, dev, etc. (prod may be the base)
  - Can import other requirement file: `-r requirements-prod.txt`
- For library project: `pyproject.toml`
  - Don't have full control of the environment --> best not pin version too hard
    - Need to test your project with supported libraries --> may use test tool "**tox**"
- Dependency resolution: pip use "backtracking" resolution (i.e. download a version, if it's in conflict, then download another version)

## Beyond Pip: pipenv and poetry

- Alternative to pip: `pipenv` and `poetry`
- Reasons:
  - Replace both `pip` and `venv`
  - Use "dependency locking" --> more deterministic builds
  - Security with dependencies (using hashes)
- Dependency management: two problems: under or over specified
  - Under-specified: don't define sub-dependency --> non-deterministics
  - Over-specified: define everything --> too strict about unimportant sub-dependencies ==> may conflict with adding packages later, or maintain version for security reason
- Dependency locking: maintain 2 lists:
  1. top-level dependency declarations
     - We care about these
     - Don't have to be pinned
  2. Installed & tested versions
     - Lock file with "*Pinned Versions*" & hashes--> managed by tools

### pipenv

- Overview: focused on applications (i.e. project where you control the deployment)
  - Use same dependency resolver as `pip`
  - "Pipfile" for top-level dependencies
  - "Pipfile.lock": locked versions with hashes
- Usage
  - Install with: `pipx install pipenv`
  - Initialize: `pipenv install <pkg-name>` --> create virtual env. bound to project name, Pipfile and Pipfile.lock
    - If project name changed, the virtual env. will be "orphaned"
    - Pipfile.lock is regenerated after each install --> slow as it needs to "lock" all dependencies anew
  - Install dev-only package: `pipenv install -d <pkg-name>` --> will update Pipfile & Pipfile.lock
  - Re-create the environment after git clone: `pipenv sync` --> recreate the environment as in lock file
    - `pipenv graph`: show the dependency tree
    - `pipenv sync -d`: recreate the "dev" environment
- To run the virtual env:
  - To "activate" the shell (i.e. sub-process of current shell): `pipenv shell` --> "end" with `exit`
  - To run a script using the virtual env.: `pipenv run python script.py`
  - To run a application/utility installed in the virtual env: `pipenv run black script.py`
- Update package
  - Check for security update: `pipenv check`
  - To update a package: `pipenv update <pkg-name>`
  - To update everything: `pipenv update`

### poetry

- Overview: for libraries & applications
  - Custom dependency resolver --> claim to be faster
  - Supports building & distributing packages
  - "pyproject.toml" for top-level dependencies
  - "peotry.lock": locked versions with hashes
- Usage:
  - Install with: `pipx install poetry`
  - New project: `poetry new <project-name>` --> generate complete project skeleton: pyproject.toml, README.md, folder for source & unittest
    - Initialze a pyproject.toml in an existing project: `poetry init`
  - Install package: `poetry add <pkg-name>` --> can use pip version specifier
    - Use its own "version specifier" in its own section within pyproject.toml
  - Uninstall package: `poetry remove <pkg-name>` --> remove this packages & its dependencies
  - Re-create after git clone: `poetry install` --> recreate the environment
    - `poetry show` & `poetry show --tree`: show the installed packages
  - Re-sync after git update: `poetry install --sync` --> uninstalled removed packages
  - Build wheel file: `poetry build`
  - Publish library: `poetry publish` --> need the URL & login into the published repository
- To run the virtual env: poetry install project in editable mode --> file change take effect immediately
  - Run script: `poetry run <runnable-command>`
  - Start poetry shell: `peotry shell` --> end shell with `exit`
