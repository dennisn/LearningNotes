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
  
## Generics
  - Let you re-use algorithm in a strongly typed way
  - Constraint the generic types with "where" keyword
  
## Immutable and Read-Only
  - Immutable: make type thread-safe, with some performance benefit (e.g. during reference parameter passing)
  - To make a class Immutable:
    + Remove "setter" of properties
	+ Mark fields as "`readonly`"
	+ Change methods that change internal properties to return a new object with updated properties instead
  - For structs (value types): makes it immutable by declare it as "`readonly`"
    + *Best practice 1*: `struct` should be made immutable --> avoid subtle bugs involve "boxing"
	+ *Best practice 2*: method in struct should be marked as "`readonly`" if they don't change the struct state --> might gain performance benefit in some cases 
  - How to expose collection as read-only:
    + can't just return a new readonly list for each call to "get": performance & subtle problems from multiple readonly object created
	+ Correct solution: create one read-only collection wrapper and exposes that as a property
	  ```
	  private List<Point> _vertices = [];
	  
	  public IReadOnlyList<Point> Vertices {get; private init; }
	  
	  public XXXConstructor() 
	  {
		Vertices = _vertices.AsReadOnly();
	  }
	  
	  ```
	+ Alternatively: provide an enumerator --> reduce functionality to client as they won't have access to whole list
	
## Data-driven coding and patterns
  - Data-driven code: program logic depends on the data
  - `switch` expression: "<variable> swtich { <case-value> => result, <case-value> => result, _ => <default-result> }"
    + matching the variable to case-value, then return the expected result
  - `switch` limitation: can only use with const ==> may need customised "rule-class"

## Events
  - use pre-made delegate: `delegate void EventHandler(object? sender, EventArgs e)`
  - null check delegation call: `<Delegate-Name>?.Invoke()`
  - Adding/Removing handlers:
    ```
	<object>.<handler-name> += <method>
	<object>.<handler-name> -= <method>
	```
  - Passing data: create class derived from "EventArgs" with extra properties to stored passing data
    + Will also need new delegate where the argument is the derived-class above, as the following example
	```
	public delegate void PriceChangedEventHandler<object sender, PriceChangedEventArgs e);
	
	public class PriceChangedEventArgs : EventArgs { ... }
	```
  - For multiple properties changed: `System.ComponentModel.INotifyPropertyChanged`, which will pass the property name in `PropertyChangedEventArgs`

## LINQ
  1. Remove Duplicates
    - Use `Distinct()` method, but need equality comparer
  2. Group Data-driven
    - Use `GroupBy(e => e.<Group-Property>)`--> return IEnumerable<IGrouping<TKey, TElement>>
	- To ensure grouping data is in correct order, could use "OrderBy().ThenBy()..." before do GroupBy()
  3. Flatten data
    - Use `SelectMany(grouping => grouping, (grouping, elements) => elements)`
  4. Join multiple lists
    - Use `L1.Join(L2, itemL1 => itemL1.Key, itemL2 => itemL2.Key, (itemL1, itemL2) => <Result-Item>)`
	- Use `GroupJoin()` with same arguments if want to group by L1 after join
	  + Calculate average within each group and order the group by average (see example below)
	    ```
		students
			.GroupJoin(
				examResults, 
				student => student.AnonymousId,
				exameResult => exameResult.StudentId,
				(student, examResult) => (student, exameResult.Average(result => result.Makr)))
			.OrderBy(tuple => tuple.student.Name)
		```
  5. LINQ extension methods: 
    - Normal extension method, but must accept as first parameter an `IEnumerable<T>`
	- Return `IEnumerable<T>` --> support "fluent" syntax
	- Using `yield` to return each element ==> support lazy evaluation
  6. Lazy evaluation
    - In LINQ, results are enumerated only when they are consumed (lazy by default)
	- When storing result in list/dictionary/single value, (i.e. not "IEnumerable") then results are all enumerated
	  + This is useful when you're likely to reuse them --> cache results into a collection

## Exceptions and Error Handling

  - Include exception variable if you need it
1. Multiple catch blocks:
  - Most specific first, to most abstract
  - Include a "catch-all" handler at the end: `catch (Exception)`--> to catch any unhandled exceptions
2. Custom exception
  - Best to derived from System.Exception, and include a constructor with innerException --> to be used if the exception is thrown from inside a catch block
3. Exception filters
  - Catch exception with exposed property of specific value: using `when`
  - Example: `catch (Exception ex) when (ex.PropName == "<some-name>")`
4. Exception from Async code
  - Async methods that return `Task<T>` or `Task`, will automatically transfer of exception to the main thread-safe
  - NOTE: return void --> bad practice as exception won't go to main thread
5. Debug exception in Visual Studio
  - VS won't stop for handled exception
  - Exception settings: Debug -> Windows -> Exception settings: choose to break when selected exception is thrown
6. Exceptions vs. Debug.Assert()
  - `Debug.Assert()`: will always invoke the debugger --> best to detect buggy code before production

## Attributes and Reflection

1. Marking a method as Obsolete
2. Custome Attributes
3. Using Reflection to find Attributes on a Type

## Async Programming

## Testing

## Interop
