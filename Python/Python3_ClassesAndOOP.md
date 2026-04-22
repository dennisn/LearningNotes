# Classes and Object-oriented Programming in Python 3

- Course URL: <https://app.pluralsight.com/ilx/video-courses/python-3-classes-object-oriented-programming/course-overview>

## Everything is an Object

- Class: object factory (i.e. also an object)
  - Objects has built-in `__class__` attribute --> its class factory (for "class" object, return the metaclass)
  - Variable is just reference to an object, which know its own class --> variable can be re-assigned to different object of different class !
- OO paradigm --> not suitable for all
  - Small script --> not needed
  - Machine Learning --> maybe functional programming

## Instantiating custom classes

- Objects are dictionaries internally --> attributes should be access via "dot" notation
  - Class instantiation: calling `__new__()`: allocation memory, then `__init__()`: initiate its attributes
- String representation of object:
  - `__str__(self)`: for print out --> end user
  - `__repr__(self)`: official string --> mostly for developers ==> running `eval()` with the official representation should return an equivalent object
- "Dunder method": double-underscore --> special methods that are meant to call indirectly through some other means/syntax (e.g. operator)

Example:

```python
class Employee:
  def __init__(self, name, age):
    self.name = name
    self.age = age

  def __repr__(self):
    return (f"Employee("
      f"{repr(self.name)}, "
       f"{repr(self.age)})"
    )
```

## Managing Attribute Access

- Two Pillars of OOP: Abstraction & Encapsulation
  - "**Abstraction**": hide the implementation details --> only see the interface
  - "**Encapsulation**": group related data & methods into logical unit: instances --> should hide data attributes that are only used for internal implemenation purposes
- Private attributes --> convention: pre-pend with one underscore.
- Name mangling: pre-pend with **two** underscores
  - Its name is the same with the class copre, but from outside, the name is different
  - **Mangling** : pre-pend with "_[class-name]" ==> prevent access
- `@property`: decorator to make automatic attribute getter
  - For "setter": use decorator `@[attribute-name].setter` on the setter method with the "same" name as the property

  ```python
  class Employee:
    @property
    def salary(self):
        return self._salary
    
    @salary.setter
    def salary(self, value):
        self._salary = value
  ```

## Implementing Class Inheritance

- Target "DRY": Don't Repeat Yourself --> motivation to "extract" common attributes & methods of classes into "parent" class ==> **Inheritance**
  - Syntax: `class DerivedClass(BaseClass):`
- Method override: only need to have the method with the same name (*even if the params are different !*) ==> **Polymorphism**
  - To specifically call method of parent class: `super().parent_method_name()`
- **Slots**: define all attributes for a class up-front --> `__dict__` is nolonger used to store attributes, but `__slots__` instead
  - Benefits: faster attribute access, reduce memory overhead --> useful when there are "a lot of instances"
  - Dis-advantage: can't add attributes dynamically
  - Example: `__slots__ = ("name", "age", "salary")`
  - For inheritance, only define the attributes defined within sub-class
    - If parent class doesn't use "slots", child class can't use it either
- Multiple inheritance: `class Developer(SlotsInspectorMixin, Employee):`
  - Method resolution order --> defined in `__mro__`: return the tupe of order in which the methods will be searched for
  - Normally, will look into subclass first, then by the declared order of parent classes
  - May "invalidate" slot if some parents don't use slots (NOTE: class without attribute still need empty slot: `__slots__ = ()`)

## Accessing Class Attributes and Methods

## Using Data Classes
