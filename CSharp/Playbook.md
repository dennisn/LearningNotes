# C# Playbook

## Control Flow and Loops

  -  For and ForEach
    - `foreach`: prefer
    - `for`: for when the index is used, or when the list is modified
  - Loops with heavy processing of items & complex looping logic (e.g. `for` and `while`)
    - Maybe better to split the "looping" logic from the "processing" logic
	- Split by put the loop logic in method return IEnumerable, utilise yield return)