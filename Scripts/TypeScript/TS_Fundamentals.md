# TypeScript Fundamentals

- Based on TypeScript 5.

## 2. Getting Started

- Install Node.js, then use it to install TypeScript.
- In VS Code:
  - Create default TypeScript config with `tsc --init`.
  - Optional config in `tsconfig.json`: `"outDir": "./js"`.
  - Create a TypeScript compile task:

    ```json
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

- Create a VS Code launch task that references the TypeScript compile task:

  ```json
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
    "preLaunchTask": "Compile TypeScript",
    "internalConsoleOptions": "openOnSessionStart"
  }
  ```

- Official docs: <https://www.typescriptlang.org/docs>

## 3. Basic Syntax and Data Types

- Basic data types: `boolean`, `number`, `string`, `array`, and `enum`.
- Variable declaration: `let someString = 'Hello World';`
  - Hoisting: in normal JavaScript, variable declarations are moved to the top of the block, which can be confusing; TypeScript helps avoid common hoisting pitfalls.
- Constant declaration: `const someString = 'Hello World';`
- Type annotation: `let x: string = 'I will forever be a string';`.
  - Type annotation is optional because the compiler can infer types from declarations, but explicit types are often a good practice.
- Other built-in types: `void`, `null`, `undefined`, `never`, `any`.
  - `void`: return type for functions that do not return a value.
  - `null` and `undefined`: special types.
  - `never`: for values that never occur (for example, functions that never return).
  - `any`: can hold any value (no type checking). Useful with third-party libraries that do not guarantee specific return types.
- Union types: define multiple allowed types for a variable.
  - Example: `let someValue: number | string;`
- `--strictNullChecks`: `null` and `undefined` cannot be assigned unless explicitly included in the type.
- Arrays: two declaration styles.
  - Example 1: `let strArr1: string[] = ['here', 'are', 'strings'];`
  - Example 2: `let strArr2: Array<string> = ['more', 'strings', 'here'];`
  - Example 3: `let anyArr: any[] = [42, true, 'banana'];` (mixed-type array)
- `for` loop: `for (let i = 1; i <= 10; i++) {}`
  - Loop through arrays: `for (const name of cast) {}`
- `while` loop:

  ```ts
  let i: number = 1;
  while (i <= 10) {
    i++;
  }
  ```

- `switch` conditional:

  ```ts
  let fruit: string = 'apple';
  switch (fruit) {
    case 'apple':
      // ...
      break;
    default:
      // ...
  }
  ```

- Check type: `typeof something === 'string';` returns the runtime type.

## 4. Functions

- Function type annotation: `function funFunc(score: number, msg: string): string {}`
- `--noImplicitAny` compiler option: reports an error when parameters are implicitly inferred as `any`.
- Optional, default, and rest parameters:
  - Optional: must appear after required parameters, for example `age?: number`.
  - Default (treated as optional if after required parameters): `title: string = 'Some string'`.
  - Can use a function return as default: `title: string = GetMostPopularBook()`.
  - Rest parameters: variable number of additional parameters after required ones.
    - Example: `function GetBooksRead(name: string, ...bookIds: number[]) {}`
- Arrow function (`lambda`): `parameters => function-body`.
  - Example: `let squareit = (x: number): number => x * x;`
- Function overloads: same function name with different parameter types.
  - JavaScript does not have type annotations, so TypeScript can use type guards to determine which overload was called.
  - To emulate overloads, declare multiple signatures and one catch-all implementation.
- Function types: combination of parameter types and return type.
  - Example: `let releaseFunc: (someYear: number) => string;`
  - Matches `function SomeFunc(year: number): string {}`.
  - Then assign and call: `releaseFunc = SomeFunc;` and `let msg = releaseFunc(2024);`

## 5. Interfaces

- Interface: a contract that defines a type checked by the compiler; it is a collection of methods and properties.
  - A variable declared as a specific interface should have the required methods and properties.
  - Duck typing: if an object satisfies a type definition, it can be used as that type even if not explicitly declared as that type.
  - TypeScript interfaces are removed when compiled to JavaScript.
  - Example:

    ```ts
    interface Book {
      id: number;
      title: string;
      pages?: number;
      markDamaged: (reason: string) => void;
    }
    ```

- Function type via interface (function pointer style).
  - Example function pointer: `let idGenerator: (chars: string, nums: number) => string;`
  - Interface example:

    ```ts
    interface StringGenerator {
      (chars: string, nums: number): string;
    }
    ```

- Extending interfaces: `interface Encyclopedia extends LibraryResource, Book {}`

## 6. Classes

- Classes in JavaScript are similar to other languages.
- Constructors: method named `constructor`; max one per class.
  - Example: `constructor(title: string, publisher?: string) {}`
- Properties:

  ```ts
  class ReferenceItem {
    numOfPages: number;

    get editor(): string {
      // custom get logic
      return 'editor';
    }

    set editor(newEditor: string) {
      // custom set logic
    }
  }
  ```

- Parameter properties: shorthand to declare and initialize class properties in the constructor.

  ```ts
  class Author {
    // Creates property `name` and initializes it at construction.
    // Access modifier can be `public` or `private`.
    constructor(public name: string) {}
  }
  ```

- Note: class members are accessed with `this.` inside class methods.
- Static properties: `static description: string = 'A source of knowledge';`
  - Access static members through the class, not the object.
- Access modifiers: default is `public`; others include `private` and `protected`.
- Newer feature: private fields using `#` prefix for stronger runtime privacy in modern environments.
- Extending classes: use `extends`.
- Abstract classes: add `abstract` before class or method names.
- Class expressions: classes without a name.
  - Example:

    ```ts
    let Musical = class extends Video {
      printCredits(): void {}
    };
    ```

## 7. Organizing Code with Modules

- Why modules: create higher-level abstractions, hide implementation details, and improve reusability.
  - Use compiler options to specify JavaScript module format: `AMD`, `CommonJS`, `UMD`, `System`, `ES2015`, etc.
  - You may also need a module loader or bundler: `RequireJS`, `SystemJS`, `Webpack`.
- Exporting: prefix interface/function/class with `export`.
  - `export default` marks the default export from a module.
  - Export multiple entities: `export { Person, methodName, Employee as StaffMember };`
- Importing: use `import`.
  - Example: `import { Person, methodName } from './person';` (relative reference)
  - Default import: `import Worker from './person';`
  - Alias import: `import { StaffMember as CoWorker } from './person';`
  - Import whole module: `import * as HR from './person';` and call `HR.methodName();`
- Module resolution: file extensions can be omitted.
  - Relative import: use `/`, `./`, `../`.
  - Absolute import: do not include directory path before module name.
  - Tip: use relative imports for your own modules.
  - `--moduleResolution Classic | Node`: two resolution modes (`Classic` is mostly for backward compatibility).
    - `CommonJS` defaults to `Node`; others may default to `Classic`.
    - `Node`: walks up directories looking for `node_modules` and may read `package.json` for configuration.
    - `Classic`: walks up directories looking for a matching module near the importing file.
- Convert an application to use modules.

## 8. Asynchronous Code

- Benefits: better responsiveness and efficiency, especially for slow tasks.
- `Promise`: represents eventual completion or failure of an async operation and its resulting value.
  - Native support in ES2015; conceptually similar to `Task` in C#.
  - Basic example:

    ```ts
    function doAsyncWork(resolve: (v: string) => void, reject: (r: unknown) => void) {
      // perform async tasks

      if (success) resolve(data);
      else reject(reason);
    }

    let p: Promise<string> = new Promise(doAsyncWork);

    p.then(stringResult => console.log(stringResult))
    .catch(reason => console.log(reason));
    ```

- `async`/`await` syntax is similar to C#.
  - Example:

    ```ts
    async function doAsyncWork() {
      let result = await GetDataFromServer();
      console.log(result);
    }
    ```

## 9. Generics

- Very similar to C#.
- Constraints use `<T extends ClassOrInterfaceName>` instead of plain `<T>`.

## 10. Type Declaration Files

- Type declaration (definition) files are wrappers for JavaScript libraries and are used by development tools.
  - Extension: `.d.ts`
  - Often included with the library, or provided via DefinitelyTyped.
  - Install from npm as `@types/<name>`.
- JavaScript libraries are available on <https://www.npmjs.com>.
- Example:

  ```bash
  # install lodash library
  npm install lodash

  # install lodash type definition
  npm install @types/lodash
  ```

## 11. Decorators

- A decorator is a function applied to other code in your application.
  - It can effectively replace an existing method with a new method.
  - Similar to attributes in C# or annotations in Java.
- Example:

  ```ts
  // Decorator implementation for `logMethodInfo`
  export function logMethodInfo(origMethod: any, _context: ClassMethodDecoratorContext) {
    function replacementMethod(this: any, ...args: any[]) {
      console.log(`Decorated construct: ${_context.kind}`);

      const result = origMethod.call(this, ...args);
      return result;
    }

    return replacementMethod;
  }

  // Use decorator on method `printItem()`
  @logMethodInfo
  printItem(): void {
    // ...
  }
  ```

## 12. Debugging

- Source maps map TypeScript source to JavaScript output, so you can set breakpoints and watches in TypeScript source.
  - Enable with compiler option `--sourceMap`, or set `"sourceMap": true` in `tsconfig.json`.
- To debug in Chrome DevTools, you need:
  - JavaScript generated with source maps enabled.
  - An HTML page that references generated JavaScript (not TypeScript directly).
  - A button or trigger to execute the TypeScript function.
  - In DevTools `Sources`, you can set breakpoints and watches similarly to VS Code.
- More depth on Chrome DevTools: "Debugging Sites with Chrome DevTools" by Brice Wilson.
