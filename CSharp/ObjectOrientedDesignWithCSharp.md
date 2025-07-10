# Object-Oriented Design with CSharp
  - Based on Pluralsight course by Mel Grubb
  - Using mermaid for class diagram in markdown (supported by github)
  - Repo: https://github.com/melgrubb/ps-oo-cs10

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
  - In depth study: "SOLID Principles for C# Developers" by Stve Smith
  - Single responsibility: a module should have a clearly-defined job
    + related to Abstraction & Encapsulation
    + Example: A car state only change for make, model, running status (e.g. car-related concepts), not for "lower-level" concepts like brake, throttle, engine
  - Open/Close principle: should be able to extend a class behavior without modifying it --> 
    + With inheritance: where each implementation variation should not impact existing one
    + With composition: new component can be added without changing the "owner" code
    + Design tips: separate "bedrock" code into base classes, while specializations on top of that bedrock into subclasses
      - If class has multiple "modes", switching its behavior --> signs to break them into smaller classes
  - Liskov substitution principle --> Polymorphism
    + Not only inter-change, but also ensure the substitution "**work/behave**" inter-change-able
  - Interface Segregation Principle: make fine-grained interfaces that are task-specific
    + Interface: group related functionality together --> keep interfaces cohesive
  - Dependency Inversion Principle: depend on abstractions, not on concretions
    + Class: should not building dependency themselves --> either set via constructor or properties

## 5. Intro. to Design Patterns

## 6. Nullability

## 7. Equality, Immutability, and Record Types

## 8. OO Examples

## 9. Putting it all together