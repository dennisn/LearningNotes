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

## Beyond Pip: pipenv and petry
