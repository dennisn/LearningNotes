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