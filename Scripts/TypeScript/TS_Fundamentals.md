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
  - Interface: contract that define a type, which can be checked by compiler --> collection of methods & attributes 
    + variable declared to be of specific interface ==> should have the exact methods & attributes (**no extra**)
    + "Duck-typing": object satisfied the type definition, can be used as if it is of that type, even though it was not explicitly declared itself to be of that type
    + NOTE: JavaScript doesn't have anything equivalent to interface, so they will be removed when compiled to JavaScript
    + Example:
      ```
      interface Book {
        id: number;
        title: string;
        pages?: number;
        markDamanged: (reason: string) => void;
      }
      ```
  - Function type (i.e. function pointer): define the parameters & return value of a function --> as a interface
    + Example: `let IdGenerator: (chars: string, nums: number) => string;` --> function pointer
    + Interfaces of Function Type example:
      ```
      interface StringGenerator {
        (chars: string, nums: number): string;
      }
      ```
  - Extending interface: `interface Encyclopedia extends LibraryResource, Book {}`

## 6. Classes
  - Classes in JS: similar to others language
  - Constructors: method named "constructor" - max of one per class
    + Sample: `constructor(title: string, publisher?: string) {}`
  - Properties:
    ```
    class ReferenceItem {
      numOfPages: number;

      get editor(): string {
        // custom get logic, return a "string" value        
      }
      set editor(newEditor: string) {
        // custom set logic
      }
    }
    ```
    + Parameter properties: a short-hand way to declare & init. a class properties in the constructor
      ```
      class Author {
        // create property "name", which is set at construction
        // access modifier can be "public" or "private" --> the modifier will apply to the property
        constructor(public name: string) {} 
      }
      ```
    + Note: all class members must be prefixed with `this.` when access from within the class
    + Static properties: `static description: string = 'A source of knowledge';` --> must access it via the class, not object
  - Access modifier: by default, all are "public"
    + Other is "private" and "public"
    + Newer feature: private field, by putting a `#` symbol in front of field names --> in newer environment, will have extra protection
  - Extending classes: using `extends` keyword
  - Abstract classes: add `abstract` keyword infrom of the class/method name
  - Using class expression: class without name
    + Example:
      ```
      let Musical = class extends Video {
        printCredits(): void {}
      }
      ```

## 7. Organizing your code with Modules
  - Why Module: Create higher-level abstractions to hide implementation details, while enhance reuse-ability
    + Use compiler option to specify JavaScript module format: AMD, CommonJS, UMD, System, ES2015, etc.
    + Also need a module loader/bundler to help load the modules: RequreJS, SystemJS, Webpack
  - Exporting: prefix interface/function/class with the keyword `export`
    + If use `export default` --> mark it as the default item exported from this module
    + Can export multiple entities: `export { Person, methodName, Employee as StaffMember };`
  - Importing: witm `import` keyword
    + Sample: `import { Person, methodName } from './person';` --> relative reference
    + Import default item: `import Worker from './person';` --> import the default item from person as "Worker"
    + Import with alias: `import {StaffMember as CoWorker } from './person';`
    + Import the whole module: `import * as HR from './person';` -->  can then call method from person as `HR.methodName();`
  - Module resolution: can leave off the extension
    + Relative import: direct the path with prefixes "/", "./", "../"
    + Absolute import: don't include any reference to a directory structure before the module name
    + TIPS: use relative import for own module
    + `--moduleResolution Class | Node`: two basic mode for resolution ("Classic" is for backward compatibility only)
      - CommonJS module is default to "Node", while others is "Classic" --> mostly apply to absolute references
      - Node: walk up the directory tree, looking for a folder named "node_modules" to find the reference file
        + It also read a `package.json` file if present for more configuration
      - Classic: walks up the directory tree looking for a module starting in the location of the importing file
  - Convert an application to use module

## 8. Asynchronous code
  - Asynchronous benefit: more responsive, more efficient, especially with "slow" tasks
  - `Promise`: represents the eventual completion or failure of an asynchronous operation and its resulting value
    + native support in ES2015 --> similar to `Tasks` in C#
    + Basic example:
      ```
      function doAsyncWork(resolve, reject) {
        // perform async tasks

        if (success) resolve(data); // data is return data
        else reject(reason);
      }

      let p: Promise<string> = new Promise(doAsyncWork);

      p.then(stringResult => console.log(stringResult))
       .catch(reason => console.log(reason));
      ```
  - async/await syntax: similar to how it was used in C#
    + Example
      ```
      async function doAsyncWork() {
        let result = await GetDataFromServer();
        Console.log(result);
      }
      ```

## 9. Generics

## 10. Type declaration files

## 11. Decorators

## 12. Debugging