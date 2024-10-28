# Some basic Software Engineer principle

## Object-oriented

### Four pillar of OO

	- Abstraction: Hide the details implementation, show the public interface => Ease of implementation change
	- Encapsulation: Group the data and the operation together => Avoid unexpected data change, easier to debug
	- Inheritance: Re-use of common behavior in class of similar types => code reuse
	- Polymorphism: Objects of common super class can be used the same way as its super class => code re-use

### Five Principles of OO design: SOLID

Ref: https://www.freecodecamp.org/news/solid-principles-explained-in-plain-english/

	1. The Single Responsibility Principle: a class should do one thing and therefore it should have only a single reason to change
	2. The Open-Closed Principle: classes should be open for extension and closed to modification
	3. The Liskov Substitution Principle: subclasses should be substitutable for their base classes
	4. The Interface Segregation Principle: many client-specific interfaces are better than one general-purpose interface => Clients should not be forced to implement a function they do no need.
	5. The Dependency Inversion Principle: our classes should depend upon interfaces or abstract classes instead of concrete classes and functions.