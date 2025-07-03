# Reflection
  - Sample code: https://github.com/KevinDockx/CSharpReflection

## Reflection basic
  - Basic structure: Assembly --> Modules --> Types --> Member
  - Assembly is the fundamental units of deployment, version control, security, etc.
    + Form: executable files, .EXE or DLLs (dynamic-link libary files)
  - Module: a portable executable file, such as type.dll, or application.exe
    + Most of the time, 1-assembly has 1-module
  - Type: collections of members (properties & methods)
    + Example: class, struct, enum, etc.
  - Member: represents the data & behavior of a type
  - Reflection is the process by which a computer program can observe and modify its own structure and behavior
  - Binding is the process of locating the declaration, that is the implementation, that corresponds to a uniquely specified type.
    + In lay term, binding: the process of resolving a name of type, method, field, etc to its corresponding implementation at runtime
    + Early binding (compile-time binding): the compiler resolves method and field calls during compilation
    + Labe binding: Objects are dynamic, where  the resolution of methods and fields happens during program execution
    + Binding flag: control binding & how reflection searches
  - `MemberInfo` class: at the root of most reflection class

### Use case
  - Dependency Injection
  - Calling private/protected member
  - Serialization
  - Type inspector application
  - Code analysis tools

### Consideration
  - Relatively slow
  - Security is merely a suggestion (e.g. private/projected is bypass)
  - Error-prone: no type checking from compilation
  - Reflection is complex

## Instantiate & Manipulate objects
  - From "constructor", we can create new object by calling `Invoke()`
    + Binder: customised selector of matching method --> using "null" for default
  - `Activator` is a class that contains methods to create types of objects locally or remotely or obtain references to existing remote objects
    + Advantage: don't need the actual type, but assembly name & fully qualified name of the type
    + Disadvantage: return an ObjectHandle, which need "`Unwrap()`" to get the actual object
      - This is because the "Activator" may not load the assembly yet (i.e. not having metadata about the object's type yet)
  - `dynamic`: use "late-binding" --> compiler won't check
  - Get/Set properties & field: by first retrieve the property/field, then call `SetValue(objectToSet, newValue)`
    + can use `InvokeMember()` directly on the type, with `BindingFlags.SetProperty` --> but loss access to specific behavior of specific member type 
  - Similarly, we can invoke method, or using "InvokeMember()" to indirectly invoke method from the parent type
    + `Convert.ChangeType()` is also needed to cast object to type --> 

## Refection with Generics
  - Generics advantage:
    + Type safety: compiler can do type check
    + Reusability: generic class can be used on variety of type
    + Performance: avoid boxing/unboxing to/from `Object`
  - Generic type name: suffix is the number of type parameters to be bound
    + `List<T>` == "**System.Collections.Generic.List`1**"
    + `Dictionary<TKey, TValue>` == "**System.Collections.Generic.Dictionary`2**"
  - Generic opened/closed types
    + Opened type: in generic format, not yet bounded to its type(s) --> can't initiated
    + Closed type: bound to its type(s) --> now can be initiated
    + Example:
      ```
      var listType = typeof(List<>);  // this is open type
      var listTypeOfString = listType.MakeGenericType(typeof(String));
      var listInstance = Activator.CreateInstance(listTypeOfString);
      ```
    + Similarly, generic method need to bound via `MakeGenericType()` before it can be called

## Advanced Topics
  - Security implication
    + **CAS (Code Access Security)**: different levels of trust for your code --> to protect system from malicious code
    + CAS is no longer supported --> `SecurityCritical` attribute and `ReflectionPermissionFlag` enumeration will have no effect anymore
    + **Reflection-only context**: loading assemblies for inspection but not execution --> gone
  - Libary `ReflectionMagic`: use custom `DynamicObject` under cover to helps writing cleaner "reflection" code
    + Example: `person.AsDynamic()._aPrivateField = "Update private field";`