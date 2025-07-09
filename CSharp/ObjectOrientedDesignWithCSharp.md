# Object-Oriented Design with CSharp
(based on Pluralsight course)

## 2. Intro. to OO Design
  - Four Pillars:
    + Abstraction: Implementation Hiding
    + Encapsulation: Data hiding
    + Inheritance: object classification
    + Polymorphism: objects interchange-ability
  - SOLID principles:
    + Single responsibility: well-defined responsibility/job
    + Open-Closed: extensions point for customization
    + Liskov substitution: interchange-ability
    + Interface segregation/separation: divide task specific interfaces
    + Dependency Inversion: not tie yourself to specific implementation
  - Cohesion: how closely related the code in any grouping focused on a single concept 
    + Better to have many small, but highly-cohesive class than one large class
  - Coupling: how much one class/object is dependent on another
    + Tight coupling is more brittle: change in one class often required change(s) in other
    + Tight coupling is ok when there shouldn't be multiple implementations of a class. If there are, then it should be dependent on the interface only to make it flexible

## 3. The Four Pillars
  - Abstraction: hiding the implementation --> minimize visibility
    + Visibility == Contract: unable to change in the future
  - Encapsulation: keep data close to its use, while minimize visibility
  - Inheritance: extract commonalities to base class, while leaving type-specific code in subtype
    + Base class should not know about descendants existence
  - Polymophism: object can have multiple capabilities/roles (i.e. interfaces)

## 4. The SOLID principles

## 5. Intro. to Design Patterns

## 6. Nullability

## 7. Equality, Immutability, and Record Types

## 8. OO Examples

## 9. Putting it all together