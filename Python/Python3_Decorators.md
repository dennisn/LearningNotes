# Python 3 Decorators

- Course URL: <https://app.pluralsight.com/library/courses/python-3-decorators>

## Working with Higher Order Functions and Closures

- Function: first class objects --> can be assigned to variables, stored in collections, or passed to other function
  - Inner functions only remove after all of its references are removed --> can be returned from other function
- Higher-order functions: take in other functions or return other functions
- **Non-local variables**: not defined inside local scope, but in its enclosing scope
  - **Closure**: a function that utilizes **non-local variables**
  - Python will save "closure" variables in `__closure__` tuples --> can access 1st item via `__closure__[0].cell_contents`
- **Decorator**: is a higher-order function that takes another function as an argument, adds some "wrapper" code around it, and returns a new function with the enhanced behavior.
  - Decorator is a closure: it has a reference to a function "outside" of its scope (i.e. the main "parameter")

    ```Python
    def email_decorator(func):
        def wrapper():
            # some code
            func()
            # more code
        return wrapper

    @email_decorator
    def greeting_message():
        continue

    # equivalent to: greeting_message = email_decorator(greeting_message)
    ```

## Implementing functions Decorators

- Decorator must be re-usable with functions of varies arguments --> returned function should accept `(*args, **kwargs)`
  - Original function can be call as `org_func(*args, **kwargs)`
  - **Good practice**: return the result of the original function call
- Wrapper closure would have different meta data to original function (i.e. different name, docstring, etc.)
  - To retain original function meta data: use built-in `functools.wraps` decorator: `@wraps(org_func)`
- Some common decorators
  - `@classmethod`: turn the method into a classmethod and automatically pass it the class itself as the first parameter
  - `@property` & `@...setter`: for get & set auto-property
  - `@lru_cache(maxsize=XX)`: least recently used cache --> return the result of the last computation
    - Least recently used cache (LRU): caching algorithm for optimizing functions that are computationally expensive and often called repeatedly with the same arguments (e.g. Fibonancci)

## Using Advanced Decorator Workflows

## Decorating classes and class Decorators
