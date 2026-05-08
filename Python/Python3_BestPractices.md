# Python 3 Best Practices

- Course URL: <https://app.pluralsight.com/ilx/video-courses/python-3-best-practices/course-overview>
- Using Python 3.10, Pylint, Black, Sphinx and Mypy

## Following Python Style Guidelines: PEP8

- PEP8: about formatting, whitespace/punctuation and naming in Python
  - Ref: <pep8.org> --> easier to read with examples
  - Better off using tools to conform to PEP8: Black formatter or autopep8
- `pylint`: very configurable tools to check for styling issues, as well as errors (via static analyzer)
  - Work with various checkers (e.g. style, design, etc.)
  - `pylint --generate-rcfile > pylintrc`: generate the configuration
- `flake8`: simpler & less configurable, but gives other outputs
  - Faster, and more completed PEP8 check vs `pylint`
- `black`: format your code to fix styling issues (VS Code extension: `Black formatter`)
- `pre-commit` framework: to run all the tools above before commit code change

### Basic styles

- Indent: 4-spaces per level
- Max line length: 79 characters
- 2 blank lines between top-level functions & classes
- 1 blank line between methods inside a class
- Imports: should be on separate lines, into 3 groups
  - Standard library
  - Third-party library
  - Local appliation/library
- Naming:
  - Modules: short, lowercase
  - Classes: Capitalized
  - Functions: lowercase_with_underscores
  - Constants: ALL_CAPS
  - Non-public names: start with underscore

## Documenting your project

Document python code with Sphinx and PEP257 (docstring standard)

- Docstring: first statement of a module/function/class/method --> become `__doc__` attribute
  - Surround by 3 double-quotes
  - Phrase ending in a period, and grammatically correct
  - Docstring for methods: specify the return value

### Sphinx

- Document generator: reStructuredText -> HTML, PDF, etc
  - extract docstring from code
- Installation: pip as with other module
  - Create the `docs` folder, and run all commands from there
- `sphinx‑quickstart` : initialize the `docs` folder of a project --> initial scaffolding structures
  - Configuration is in `conf.py`
- `make html` or `make clean html`: generate the HTML documentation into `_build/html` folder
- To generate reStructuredText documentation for python package
  - `sphinx-apidoc -o docs <package-name>` from project root
  - Update `conf.py`:
    - insert source path to `sys.path`
    - add extensions sphinx.ext.autodoc and sphinx.ext.viewcode
- Can generate the full documentation with `sphinx-apidoc --full -o docs <package-name>` (i.e no need for quickstart)

### reStructuredText

- All paragraphs separated with blank line, and indentation to group text
- Section: created by header text, identified by "underlined"
  - Subsection: different "underlined" (e.g. "=" for 1st level, "-" for 2nd level)
- Code sample: end previous paragraph with "::", surround the sample with blank line, and indent them

  ```reStructuredText
  Sample code::
  
    import something
    princ("Hello")
  
  ```

- Hyperlink sample: ```The link to `Pluralsight <http://www.pluralsight.com>` for example```
  - Can just put the link as plain text
- List: can be '*', '+', etc.
- Within python docstring: (more in Sphinx --> reStructuredText --> Domains --> Python Domain)
  - reference method with ```:meth:`<method-name>` ```, where method-name: local name or fully qualified name
  - reference attribute with ```:attr:`<attribute-name>` ```
  - method parameter: prefix with `:param`
  - return value: `:return:`
  - class attribute: `:ivar`

## Improve your code with Type Checking
