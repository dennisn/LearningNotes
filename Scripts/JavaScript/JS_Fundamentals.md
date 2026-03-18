
# JavaScript fundamentals

- Pluralsight course: <https://app.pluralsight.com/ilx/video-courses/fundamentals-javascript/course-overview>
- Versions:
  - Node.js 18 LTS
  - JavaScript ES2022
  - VS Code 1.73.4
- Main online docs: <http://developer.mozilla.org/en-US/docs/Web/JavaScript>

## Syntax, Variables and Data Types

- Variables: automatic memory management
  - `let` declare variable
  - `const` declare constant
  - `var` declare variable old way --> allow redeclarion, scope through function ==> *do not used*
- Data types:
  - Primitive: immutable, stored on stack & passed by value
    - Types: Boolean, number, BigInt, String, Symbol, Null & Undefined
  - Object: mutable, stored on heap & passed by reference
- Strings: can be created with ", ' or `
  - Template literals with backtick:

    ```javascript
    fullName = `${firstName} ${lastName}`;
    ```

  - Create multi-line strings with backticks
  - Escaping characters: using "\" to escape, or mix single with double quote
- Boolean: contains true/false
- Undefined: a variable without assigned value: `let x;`
  - Null: a variable has been assign a "*null*" value: `let manager = null;` --> *`typeof(null)` will return object instead of null type !*
- Object: similar to dictionary
  - Constructor:

    ```javascript
    let obj1 = {};
    obj1.firstname = "First";

    let obj2 = new Object();

    let obj3 = {
      firstName = "David"
    };

    console.log(`First name: ${obj3.firstName}`);
    console.log(`First name: ${obj3["firstName"]}`);
    ```

  - Property name: not recommended to have space. But if has space --> use the `["<property_name>"]` syntax
  - Property could be delete: `delete obj3.firstName;`
  - Access non-existent object: return `undefined`
- Date: actually represent date & time
  - main "string" constructor: accept ISO date time format with timezone
  - Bare date string --> assume to be GMT: `2023-01-01` vs. written date string: `January 1, 2023` --> local date
  - Numerical construtor: local date-time
  - `getMonth()`: zero-indexed value
- Class: evolve from prototype

## Data Type Conversion

- To string: using backticks
- To number (from string): `Number("10");`
- Invalid conversion --> return `NaN`
- To boolean: undefined, NaN, 0 --> `false`, others --> `true`
- "**JSON**": JavaScript Object Notation --> express object literal as string ==> omit null attribute, and function
  - `JSON.stringify(obj, null, 2)`: format `obj` to string, each indent by 2 spaces
  - `JSON.parse(jsonString);`: parse `jsonString` into JS object
- *Number*: can format using `toLocaleString([locales])` and `toFixed([digits])`
  - Also use `Intl.NumberFormat` for more formatting
- *Date*: format using `toDateString()`, or `toLocaleDateString([locales])`
  - Similar with time (e.g. `toTimeString()`)

## Conditional Logic and Control Flow

- Equality check: `===` and `!==` for strict comparison (i.e. no type convertion, different type mean not equals) --> `'42' !== 42`
  - Object equality: compare the reference, not the value (NOTE: String is still compare by value)
  - Lose equality: `==` and `!=`: may result in strange result (e.g. `5 == '5'`, `0 == false`, `null == undefined`)
- Assignment operator: can prefix with other Math operator (e.g. `+=`, `**=`)
- Boolean value: `Boolean("false") === true`: all non-empty string is true
- '**switch**': same as java

## Collections and Loops

- Collections: hold multiple items in a variable
  - Array: ordered, index start from zero, and new items added using `push()`
  - Maps: dictionary with `set` and `get` for add & retrieval, `delete` for removal
  - Sets: prevent duplicated, but no ordered. New items are added with `add()`
- *ARRAY*:
  - `shift()/unshift()`: Remove/adding value to beginning of array
  - `splice(x, 0, [value])`: insert value at specific index --> new value will be at index "x"
    - `splice(x, y)`: remove "y" items from index "x"
- *MAP*: very similar to object, but keys can be value, object, even function
- *SET*: similar to Map, but don't have value
- *LOOP*:
  - Basic loop: `while() { .. }`, `do { .. } while()`, `for() { .. }`
  - looping array: `for (let val of array) { .. }`
  - looping through object properties: `for (let key in obj) { .. }` --> "key" will be property name
  - `continue` and `break`: beside normal use, could include "label" to jump to outer scope

## Functions

- Declaration;
  - Basic: `function isXXX(inputOne) {}`
  - Expression (anonymous): `const isXXX = function(input) {}`
  - Arrow function: `const isXXX = (input) => {}`
- Function can also be passed as argument to other function, especially useful with arrow function
  - Example: `const foundObj = arrayObjects.find(o => o.Id === 10);`
- Higher-order function: a function with one/more params and returns another function as its return value
  
  ```Javascript
  const isIntValid = (min, max) => {
    return (input) => {
      return input >= min && input <= max;
    }
  }
  ```

## Asynchronous JavaScript and Error Handling

- 3 ways for async: callback, promise & async/await
- error handling: try/catch, easiest with async/await
  - Promise --> has its own: `promise.then(data => {}).catch(err => {});`
- Callback
  - callback function -> at least 2 params: error & data
- Promise: specify the logic that needs to be executed once the "promise" has completed
  - logic *as function* -> 1 param: data
  - Can chain multiple promise, with one catch: `promise.then(data => {}).then(data => {}).catch(err => {});`
  - Callback API can be used to create customed Promise:

    ```JavaScript
    const promiseApi = async(param) => {
      return new Promise((resolveFunc, rejectFunc)) => {
        callbackApi.doCall(param, (err, data) => {
          if (err) {
            rejectFunc(err);
          }
          resolveFunc(data);
        });
      }
    }
    ```

- Async/await: syntatic sugar --> still use Promise
  - Use async function as a Promise: `asyncFunc().then(data => {})`
  - Later version of Node (>18): can use await outside of async function --> assume top level scope is within an async function

## Modules

## Code Formatting and Testing
