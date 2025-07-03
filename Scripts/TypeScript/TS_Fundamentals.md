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

## 4. Functions

## 5. Interfaces

## 6. Classes

## 7. Organizing your code with Modules

## 8. Asynchronous code

## 9. Generics

## 10. Type declaration files

## 11. Decorators

## 12. Debugging