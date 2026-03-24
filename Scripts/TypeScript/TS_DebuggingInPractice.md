# TypeScript in Practice: Debugging

- Course: <https://app.pluralsight.com/ilx/video-courses/typescript-5-debugging/course-overview>

## Understanding the TS Debugging Process

- Debug: identify & fix errors in code using tools
  - Debugger: special environment for code to run
  - Source Maps: generated automatically, to associates compiled code with soure code for readability when inspecting in debug mode
  - Breakpoint: special marker attached to a line of code --> code will stop at breakpoint until resumed ==> time to analyze application state

## Debugging TS applications with Source Maps and Google Chrome

- Source maps:  connect the JS code in browser to TS source code --> allow debugging & analysis, but increase file size & de-obfuscates code ==> not suitable for production
- Breakpoint: can be inline (i.e. add directly to code) or external (annotated by IDE)
  - Inline works in any *browser* environment, but insert changes directly to production code
  - External breakpoint: cleaner & more professional as not changing the code

## Debugging TS applications with the VSCode Debugger

- Source maps must be enabled in `tsconfig.json`
- Need a launch.json to configure VSCode
  - Terminal (node.js) debugging s simple
  - Web app debugging is more complex, use both VSCode debugger & browser (chrome)

  ```json
  {
    "type": "chrome",               // define which browser to use
    "request": "launch",            // whether to launch a new browser, or connect
    "name": "Debug TS in Chrome",
    "url": "http://localhost:5173/",
    "webRoot": "${workspaceFolder}/src",
    "sourceMapPathOverride": {      // meta-data about where in your workspace files can be found
        "webpack:///src/*": "${webRoot}/src/*"
    }
  }
  ```

- Debug process
  - First start the apps (e.g. `npm run dev`) --> get the actual application URL
  - Update the "url" in launch.json, then launch the debugger
