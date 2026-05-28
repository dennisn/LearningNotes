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

- Decorator with argument --> `some_func = decorator_function(some_arg="value")(some_func)`
  - The decorator factory create a new decorator with the decorator's argument, which accept the "original_function" as argument

  ```Python
  def decorator_function(some_arg):
    def _decorator_function(func):
      def wrapper():
        print(f"Some argument: {some_arg}")
        return func()
      return wrapper
    return _decorator_function

  @decorator_function(some_arg="value")
  def some_func():
    pass

  # Decorator syntax is equivalent to
  some_func = decorator_function(some_arg="value")(some_func)
  ```

- Multiple decorator can also stack: `some_func = decorator1(decorator2(some_func))`
  - Order of execution is from "top-to-bottom" by the order of declaration

## Decorating classes and class Decorators

- Class decorator: save decorator's argument in `self`, then `__call__(self, func)` will return wrapper function

  ```Python
  class DecoratorClass:
    def __init__(self, some_arg):
      self.some_arg = some_arg

  def __call__(self, func):
    def wrapper():
      print(f"Some argument: {self.some_arg}")
      return func()
    return wrapper

  @decorator_function(some_arg="value")
  def some_func():
    pass
  ```

- `property`: is a class --> lowercase name to abstract (i.e. hide) that this is a class
  - Full constructor signature: `property(fget=None, fset=None, fdel=None, doc=None)`
  - property is a Descriptor base class
- **Property explaination**:
  - `@property` --> create Class attribute with name matching the function name
  - `@x.setter` --> update the class attribute to add the set function
  - Get: `obj.x` --> equivalent to `MyClass.x.__get__(obj)` --> `x.__get__(obj, MyClass)`
  - Set: `obj.x = 2` --> equivalent to `MyClass.x.__set__(obj, 2)` --> `x.__set__(obj, MyClass, 2)`
- Decorator can also be used with class --> sample

  ```Python
  def add_speak(cls):
    cls.speak = lambda self: f"Hello, I'm a {self.__class__.__name__} instance"
    return cls

  @add_speak
  class SomeClass:
    pass
  ```

- Class decorators from standard library
  - `@functools.total_ordering`: help complete rich comparison ordering method, given a class with one or more implemented comparison methods --> may trade off with performance
  - `@dataclass`: add boilerplate methods for classes
