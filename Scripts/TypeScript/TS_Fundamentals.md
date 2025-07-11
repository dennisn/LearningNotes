# TypeScripts Fundamentals
  - Based on TypeScript 5

## 2. Getting started
  - Install NodeJs, then use it to install TypeScript
  - Using VS code, 
    + Create default TypeScript config with : `tsc --init`
      - may config: `"outDir": "./js"`
    + Create type script compile task
      ```
      {
			"label": "Compile TypeScript",
			"type": "shell",
			"command": "tsc",
			"presentation": {
				"echo": false,
				"reveal": "silent",
				"focus": false,
				"panel": "shared",
				"showReuseMessage": false,
				"clear": false
			}
		}
      ```
    + Create VS code launch task that refer to typescript compile task
      ```
        {
            "type": "node",
            "request": "launch",
            "name": "Launch Program",
            "skipFiles": [
                "<node_internals>/**"
            ],
            "program": "${workspaceFolder}\\js\\app.js",
            "outFiles": [
                "${workspaceFolder}/**/*.(m|c|)js",
                "!**/node_modules/**"
            ],
            "preLaunchTask":"Compile TypeScript",
            "internalConsoleOptions": "openOnSessionStart"
        }
      ```
  - Official document: https://www.typescriptlang.org/docs

## 3. Basic syntax & Data Types
  - Basic datatype: boolean, number, string, array and **Enum**
  - Variable declaration: `let someString = 'Hello World';`
    + Hoisting: in normal JavaScript, all variable declarations are moved to the top of the code block --> confusing ==> not happened in typescript
  - Constants declaration: `const someString = 'Hello World';`
  - Type annotation: `let x: string = 'I will forever be a string';` --> fix the type of x to be string only
    + Type annotation is optional, as compiler may infer the type from declaration --> good practice though
  - Other built-in types: Void, Null, Undefined, Never
    + Void: returned type for functions that don't return a value
    + Null and Undefined: special types that can be assigned to all other types
    + Never: assigned to value that never occur (e.g. function that will never return)
    + Any: can be assigned any value (i.e. no type checking) --> most useful when working with 3rd party library, which doesn't guarantee specific return type
  - Union types: a way of defining several types that may be assigned to a variable
    + Sample: `let someValue: number | string;`
  - `--strictNullChecks`: null & undefined can't be assigned to variable, unless their type explicitly include null or undefined
  - Arrays: two way to declar:
    + Sample 1: `let strArr1: string[] = ['here', 'are', 'strings'];`
    + Sample 2: `let strArr2: Array<string> = ['more', 'strings', 'here'];`
    + Sample 3: `let anyArr: any[] = [42, true, 'banana'];` --> mixed type array
  - Loop "**for**": `for (let i=1; i<=10; i++)`
    + Loop through an arrays: `for (const name of cast) {}`
  - Loop "**while**":
    ```
    let i: number = 1;
    while (i <= 10) 
    {
      i++;
    }
    ```
  - Conditional "**switch**":
    ```
    let fruit: string = 'apple';
    switch (fruit) {
      case 'apple':
        ...
        break;
      default:
        ...
    }
    ```
  - Checking type: `typeof(something) == 'string';` --> return the "actual" type of the value within the variable

## 4. Functions
  - Type annotation to functions: `function funFunc(score: number, msg: string); string {}`
  - the `-noImplicitAny` compilter option: --> get error if params is implicitly infered into Any
  - Optional, Default and Rest parameters
    + Optional: must appear after all required params: `age?: number`
    + Default (treated as optional if it's after required params): `title: string = 'Some string'`
      - Can use function return for default param: `title: string = GetMostPopularBook()`
    + Rest params: mechanism for passing a variable number of additional parameters after required params
      - Sample: `function GetBooksRead(name: string, ...bookIds: numer[])`
  - "Lambda" function: "parameters => function-body"
    + Sample: `let squareit = (x) => x * x;`
  - Function overloads: same function name, different parameter type --> JavaScript don't have type annotation ==> in TypeScript, may use type guards to determine which overload was called
    + To emulate overload, declare multiple functions with different signature, but one "catch all" implementation
  - Function Types: combination of parameter types & return type
    + Sample: `let releaseFunc: (someYear: number) => string;` --> match `function SomeFunc(year: number): string {}` ==> so can assign it: `releaseFunc = SomeFunc;`, then call it `let msg = releaseFunc(2024);`

## 5. Interfaces

## 6. Classes

## 7. Organizing your code with Modules

## 8. Asynchronous code

## 9. Generics

## 10. Type declaration files

## 11. Decorators

## 12. Debugging