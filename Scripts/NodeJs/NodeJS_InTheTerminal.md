# Node.js in the Terminal

- Course URL: <https://app.pluralsight.com/ilx/video-courses/nodejs-terminal/course-overview>

## Overview

- Can use Node.js terminal run-time to explore available facilities (i.e. types, libraries & modules)
  - Start with `node` (i.e. node REPL - Read-eval-print loop)
- No document, window object
  - a `global` object to replace some browser functions, such as : `fetch, setInterval, setTimeout`
  - `process`object --> current runtime & OS

## Discover the Node environment

- `node .`: default to run `index.js` file in specify folder (e.g. current)
  - include `--watch` option to watch the file & re-run when its changed
- Pass CLI argument to app: accessible via `process.argv`
  - This include first item as node program, but don't include node option (e.g. not include "--watch")
- Environment variable: `process.env.[VariableName]`
  - can be defined in external file specified via "env-file" option: `--env-file=settings.env`
- Good practice for asynchronous programming in single thread environment (i.e. Node.js)
  - Avoid using `process.exit()`
  - Avoid blocking code when possible

## Import Modules

- To import modules, such as `os` for operation system method
  - For old syntax: `const os = require("os");`
  - Using import: `import os from "os";` --> need existence of "package.json" file with `{ "type": "module" }`
- Conditional import: only import certain module for specific condition

  ```JavaScript
  const {default: flagActions } = await import ("./flagActions.js");
  ```

## Manage Files and Folders

- Need to import file system module: `import fs from "fs";`
- Other useful modules:
  - `url`: convert file URL to path: `url.fileURLToPath(import.meta.url)`
  - `path`: for path utilities, such as getting directory from file path: `path.dirname(fileName)`

## Create a simple HTTP server

- The base module for HTTP server is "*http*": `import http from "http";`
  - More complicated server is better use framework like "*express*", which is built on top of "*http*"

## Handle Events

- `EventEmitter` from `node:events` is the main base class for events handling
  - Node.js runtime provide `process` object, which is an extension of EventEmitter --> use `process.on()` to add event listener
  - `http.Server` object also have "on()" method to add event listener
- Custom EventEmitter object: can be used to de-couple emitters & handlers
  - register handlers on EventEmitter object: `emitter.on(eventName, listener)`
  - event producer: call `emitter.emit(eventName, [..args])`
- **NOTE**: event handler is synchronous --> can use `setImmediate()` to make callback function asynchronous

## Test your code

- Basic test example

  ```JavaScript
  import Stats from "./stats.js";       // the module to be tested
  import { test } from "node:test";
  import assert from "assert";

  test("calculates the mean of an array of numbers", () => {
    const stats = new Stats([1, 3, 5]);
    const actual = stats.mean();
    const expected = 3;
    assert.ok(actual === expected);
  })
  ```

- run test: `node --test`
  - run test with coverage check: `node --experimental-test-coverage --test`

## Build a CLI tool

- CLI tools can be built using module "*readline/promises*", especially method `createInterface()`
