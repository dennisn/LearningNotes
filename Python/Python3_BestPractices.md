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

## Improve your code with Type Checking
