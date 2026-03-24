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

## Create a simple HTTP server

## Handle Events

## Test your code

## Build a CLI tool
