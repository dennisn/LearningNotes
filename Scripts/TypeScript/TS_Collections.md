# Typescript 5: Using arrays and Collections

## Setup

### Locally with VS Code
```
# Setup the JavaScript environment ?
npm init

# Install TypeScript (globally ?)
npm install -g typescript

# setup TypeScript configuration file 
# outDir: where compiled JavaScript file will go
# rootDir: where out TypeScript file will be
# sourceMap: add mapping between TypeScript & JavaScript source file
tsc --init --outDir jsFiles --rootDir tsFiles --sourceMap

# Automatic "watch" for file change
tsc -w
```

### Web-based environment
  - StackBlitz.com
  - CodePen.io: may need to change "settings" so it won't update console while you're typing

## TypeScript Arrays
  - Collection of object of the same type (vs. JS: object of mixed types)
  - Construction/init.:
    ```
    let genericArray: any = []; # Old style:

    let newArray: Array<string> = []; # new style
    Let arrayOfNumbers: numer[] = []; # new style short-hand
    ```
  - add element to the end: `array.push(x1, .., x2);` --> return new length
  - add element to the start: `array.unshift(x1, .., x2);` 
  - remove last element: `array.pop();` vs. `shift();`: remove first element --> return removed-item, or `undefined` if array is empty
  - `array.splice(startIndex, deleteCount, ...itemsToAdd);` --> delete & add to existing array
  - Concatenate array: 
    + Using "concat()": `aOne.concat(aTwo, aThree);`
    + Using "spread" method: `const combinedArray: string[] = [...arrayA, ...arrayB, ...arrayC];`
  - Extract portion of an array: `array.slice(begin?: number, end?: number): T[]` --> if "end" < "begin", return empty array
  - Copy array to avoid side effect when modify the new array
    + `let newArr = orgArray.concat();`
    + `let newArr = orgArray.slice();`
    + `let newArr = [...orgArray] ;`
  - Sort array
    + Function `sort()`: inplace
    + Function `toSorted()`: create a new copy that is sorted --> not widely supported, but Chronium-based browser only
  - `reverse()`: reverse the order of existing array
  - Find index of an element: `array.indexOf(searchElement[, fromIndex]);` vs. `lastIndexOf(searchElement);`
    + Function `includes(searchElement[, fromIndex]);` --> return true/false only
  - Function `map()` example: `let squares: number[] = numbers.map((x: number): number => x * x);`
  - Select items matching condition: `let selection: string[] = myArray.filter(m => m.length > 3);`
  - Check if a variable is array: `Array.isArray();`
  - Loop through array with `for`:
    + Basic: `for (let i: number = 0; i < array.length; i++) { ... }`
    + for-of loop: `for (let n of numbers) { ... }`
    + for-in loop: `for (let n in numbers) { ... }` --> n is the index of the array


## Typescript Tuples

## Typescript Sets