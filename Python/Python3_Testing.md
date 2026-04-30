# Testing in Python 3

- Course URL: <https://app.pluralsight.com/ilx/video-courses/python-3-testing/course-overview>

## Unit Test Vocabulary & Design

- Unit tests: tests for small pieces of code that are part of a larger system
  - Strictly speaking: should not use any slow external resources (e.g. file, database or network)
- Module `unittest`: built-in framework, based on JUnit --> can simply run with `python -m unittest`
  - File name prefix with `test_`
  - Class is derived from `unittest.TestCase`
  - Test method prefix with `test_` --> can be skipped with `@unittest.skip`
  - Default setting & clean up: `setUp()` & `tearDown()`

## Using Pytest

- `pytest`: more native to Python, popular alternative to `unittest` --> need to install separately, run with `python -m pytest`
  - File name prefix with `test_`
  - Don't need test class
  - Same test method prefix with `test_`
  - Not using `assertXXX()` methods from TestCase, but using built-in Python `assert` --> show more details in failure
  - Check for throwing exception: need to import `pytest`
- Test Fixtures
  - To shown available fixture: `python -m pytest -fixtures`
  - Share resources is "injected" from method (1) with the same name, and (2) decorated with `@pytest.fixture`

    ```python
    @pytest.fixture
    def phonebook():
        phonebook = Phonebook()
        yield phonebook
        phonebook.clear()   # clean up "after" the test is done, equivalent to "tearDown()"

    def test_missing_name(phonebook):
        # do something with the phonebook
        with pytest.raises(KeyError):
            phonebook.lookup("MissingKey")
    ```

- Parameterized tests with `@pytest.mark.parameterize()` --> reduce duplication in test code
  - Need 2 parameters: a string describe the parameters required, and the list of tuple of parameters

    ```python
    @pytest.mark.parameterise(
        "entry1,entry2,is_consistent", [
            (("Bob", "12345"), ("Anna", "01234"), True),
            (("Bob", "12345"), ("Sue", "12345"), False),
            (("Bob", "12345"), ("Tim", "123"), False),
        ]
    )
    def test_is_consistent(phonebook, entry1, entry2, is_consistent):
        phonebook.add(*entry1)
        phonebook.add(*entry2)
        assert phonebook.is_consistent() == is_consistent
    ```

- Organise unit tests:
  - Source codes in "./src" folder
  - Unit tests in "./test" folder
  - Having a shared fixtures file to define fixtures
  - Can mark certain test as slow with `@pytest.mark.slow`, which then can be skipped with `python -m pytest -m "not slow"`
    - Other useful marker: `@pytest.mark.skip` and `@pytest.mark.skipif`
    - Use "**pytest.ini**" for more control over pytest & markers

## Testing by Developers: Why and When

## Using Test Doubles

## Improving Test Coverage and Maintainability

## Code That's Difficult to Test
