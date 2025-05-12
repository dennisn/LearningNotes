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

  - Marking a method as Obsolete: with `[Obsolete("<Description>")]`
  - Custom Attributes: derived from "System.Attribute"
    + Best practice: mark class as `sealed` to improve performance in Reflection
    + Often decorated with "AttributeUsageAttribute" to specify which code elements it can be used with
      - Example: `[AttributeUsage(AttributeTargets.Class | AttributeTargets.Struct)]`
    + Usage: remove the trailing "Attribute" from the name, also apply at compile-time
  - Using Reflection to find Attributes on a Type
    + Use `GetType()` on instance to get the Type object
    + Use `GetCustomAttributes()` to get actual attributes that have been applied to a type/code element
      - Can pass in the type of attribute needed to filter out others, with the second attribute specify whether to return attributes applied to base classes
  - Using Attributes to create friendly text for enumerated
    + Need custom attribute to contain the friendly text
    + Add a generic static method to get friendly text
      ```
      static string GetFriendlyText<T>(T value) where T : Enum
      {
        Type type = value.GetType();
        FieldInfo? fieldInfo = type.GetField(value.ToString());  // enum string is also field name
        // Get the custom attribute, and return its friendly text
      }
      ```
  - Using Reflection to get  property value of an instance
    + Use `type.GetProperties(BindingFlags.Instance | BindingFlags.Public | BindingFlags.Declaredonly)` --> get public instance declared at the type (i.e. ignore inherited)
    + Then use `prop.GetValue(instanceToCheck)` to get the property value of given instance
  - Identifying whether a class is immutable
    + For all `NonPublic | Public | Instance | Static` fields
    + Check if any not `IsInitOnly`
    + Recursively go up the hierachy with `type.BaseType` (System.Object will return null)
    + *NOTE: Not consider interface with default static field*

## Async Programming

  1. Launch simultaneous asyncs Operations
    - Calling async methods without `await` --> collect `Task<T>` objects into a list
    - Wait until all completed --> by calling `await Task.WhenAll(<my-task-collection>)`
    - Show/Process each result ASAP with `IProgress<TResult>`
      + Async method need to report the progress when they done: `progress.Report(result)`
      + Caller pass a `Progress<TResult>` with action lambda to run when a task reports progress
      + NOTE: ensure the Progress<TResult> was instantiate on the UI/main thread, then the processing of result will be on UI/main thread
  2. Thread-safe data
    - Need to sync. thread acess to prevent data corruption
	- basic mechanism is using lock
	- Alternatively, can use ConcorrentXXX class: performance is worse than normal collection class, but better than using basic lock
	  + For thread-safe way of simple arithmetic: using `System.Threading.Interlocked`: need to pass the impact object as reference
	- Best way: design code to avoid synchronisation altogether
  3. Generate & consume async stream 
    - return `IAsyncEnumerable<T>` instead of `IEnumerable<T>`
	- For reading: use `await foreach`
  - To run a synch. method async. on background thread: `await Task.Run()`

## Testing
  1. Mocking to prevent external dependency
    - External dependency can be database, external source --> slow for unittest, and hard to control/setup
	- Use mock/stub to make testing easier
  2. Static External Dependency
    - Wrap the static dependency in an interface --> allow easy mock-up
  3. Choosing parameter values to test a method
    - Use data with different features
	- Include edge case-value
    - Include invalid data
	- Alternatively, try to ensure 100% code coverage

## Interop
  - Unmanaged API: for OS functions that aren't mapped to C#
  
### Windows API
  - Parameters are marshalling from C# <==> Win32 via P/Invoke layer. Some common types:
    + HWND (windows handle) --> IntPtr/nint
	+ LPCTSTR/LPCWSTR (long-pointer string) --> String (LPCTSTR: can be ANSI and Unicode, vs. LPCWSTR: Unicode only)
	+ BOOL --> bool
	+ DWORD --> uint
	+ WCHAR --> Unicode char
  - Some API method comes in pair: XyzA and XyzW (for Ansi and Unicode). 
    + when declare, can omit 'A'/'W', and based on charset --> will automatically call the correct variation
	+ Example: `[DllImport("User32.dll", CharSet = CharSet.Unicode)]` --> for calling the Unicode variation
	+ C# is already in Unicode, so using Ansi version would loose globalisation benefit, yet lower performance because of the convertion overhead 
  - Some types from C# can't be directly mashalled into Win32 --> Need specific directive
    ```
	[StructLayout(LayoutKind.Sequential, CharSet = CharSet.Unicode)]
	struct DISPLAY_DEVICE
	{
		public uint cb;
		[MarshalAs(UnmanagedType.ByValTStr, SizeConst=32)] public string DeviceName;
		[MarshalAs(UnmanagedType.ByValTStr, SizeConst=128)] public string DeviceString;
		...
	}
	```
  - Calling VB from C#: Only need to reference the project --> As with normal C# library