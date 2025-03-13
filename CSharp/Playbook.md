# C# Playbook

## Control Flow and Loops
  -  For and ForEach
    - `foreach`: prefer
    - `for`: for when the index is used, or when the list is modified
  - Loops with heavy processing of items & complex looping logic (e.g. `for` and `while`)
    - Maybe better to split the "looping" logic from the "processing" logic
	- Split by put the loop logic in method return IEnumerable, utilise yield return)
	
## Methods and Properties
  - Guard clause: ensure method parameters are valid
    - Ease for later debug, as we detect the problem earlier on
	- May use separated method to validate: prefer "**simple**" validation to avoid having to debug the validation itself
  - Property: use simplest possible syntax
    - Backing field but no logic: use auto-property
	- No backing field: use expression-bodied property
	- Backing field and logic: use full property syntax
  - Fluent coding: use the return value from each operation to chained-calling next operation
    - Help simplify code if doing a sequence of operations on same/similar object
	- Often returning `this`: might impact performance slightly since returning a value instead of `void`.
  - Return multiple values with value tuple: should be prefered as more readable
    - consume by using "tuple deconstruction", or simply assign to a var and refer to property as object
  - Parameters passing by `in`, `out`, and `ref`: all passing by reference, only different in compiling check
    - `in`: just to improve performance (i.e. copy reference instead of copy whole struct)
	- `out`: for return value
	- `ref`: if want to modify the value given by caller
	
## Types, Objects and OOP
  - Initializese fields:
    - Run in order "Initializers" --> "base constructor" --> "derived constructor"
    - Can only use data from items that have already run
  - Lazy<T>: thread-safe lazy loaded
  - Nested class: taking on sub-responsibility of a class, while prevent its mis-use
  - Class vs struct vs record
    
## Interface
  - Can be defined at the lowest level, hence be referenced by all class
    - Avoid the dependency problem in project reference
	- Help in dependency injection & mock
  - Explicit interface implementation: good for avoid name clashes
    - Also useful to hide members not relevant to the concrete type's purpose
	
## Null values
  - Nullable<StructT> is not the same as StructT
    - Nullable class is the same as class (since classes are basically nullable)
	- Hence Nullable of class doesn't have properties like "HasValue" or "Value"
  - Default value of a struct is not the same as `null`
    - But default value of a "**Nullable**" is `null`
  - Nullable of class: won't have "HasValue" 
  - Null-forgiving operator: let VS know that the return value won't be null 
    - Example: `x.ToString()!`
  - String: can be null, empty or all whitespaces
    - Should think if the 3 values have the same/different meanings
	- If all the same, may want to convert them to `null` from the input to avoid "expensive" checking
  
## 