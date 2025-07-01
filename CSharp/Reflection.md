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

## Refection with Generics

## Advanced Topics
