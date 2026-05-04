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

- Why unittest:
  1. To ensure the code works as expected --> act as document of the code
  2. To prevent unexpected regression in the future
- Test After vs. Test Driven Development (TDD):
  - Test after: discover bugs/design problem late in the development cycle
  - TDD: interleave design test cases with design code --> result:
    - Loose coupling & High cohesion
    - Frequent chances to refactor
- Continuous integration: ensure "shared" unittests are all passed --> alert teams to potential problems (i.e. tests outdated, ..)

## Using Test Doubles

- Test Double: "fake" objects that mimic the real dependency --> control the test environment
- **Dummy**: a place holder that is not used --> often a sign that parameter should be optional with default
- **Stub**: same interface, "no implementation" but what you put there
- **Fake**: same interface, but "simple implementation" not suitable for production (e.g. file IO vs. string IO, Db vs in-memory Db, etc.)
- **Spy**: similar to **Stub**, but records the method calls & parameters it received --> can make assertion about method calls
- **Mock**: similar to **Spy**, but will assert & throw error if expected method calls are not correctly invoked

## Improving Test Coverage and Maintainability

- `approvaltest`: test verification library by comparing the actual output against previously approved result
  - Normal "assertion": for small & quick unittest vs. `approvaltest`: complex, large object/legacy code
  - Important of Printer design
    - Layout text on multiple lines --> easier to see diffs
    - Exclude irrelevant details
  - Has facility to generate test cases for all "combination" of parameters --> used to increase unittest coverage with small amount of codes
    - May quickly increase the # of test cases & test time because of overlapped
  - All-Pairs: another facility to try optimise the "combination" of parameters to reduce overlapped
- `coverage`: package to show coverage of unittests
  - Shows which lines are test
- Branch coverage: different to code coverage, this specifically about cases at conditional statements (e.g. `if` statements)
  - Mutation testing: insert a bug to see if existing unittests fail --> identify test improvements
  - Very time & resource intensive --> may not be feasible

## Code That's Difficult to Test
