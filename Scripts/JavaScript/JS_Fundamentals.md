
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

- Equality check: `===` and `!==` for strict comparison (i.e. no type convertion, different type mean not equals)
  - Lose equality: `==` and `!=`: may result in strange result (e.g. 5 -= '5', 0 == false, null == undefined)

## Collections and Loops

## Functions

## Asynchronous JavaScript and Error Handling

## Modules

## Code Formatting and Testing
