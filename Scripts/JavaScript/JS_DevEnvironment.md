# Building a JavaScript Development Environment

- Pluralsight course: <https://app.pluralsight.com/ilx/video-courses/javascript-building-development-environment/course-overview>

## Integrated Development Environment (IDE)

- IDE: editor with "*intrgrated*" tools for development works (e.g. build, debug, syntax highlight)
  - Contextual help (e.g. auto-complated)
  - File management & source-code control
  - Debug capabilities
  - Customizable working environment (e.g. hotkeys, layout)
  - Add functionalities with extensions
- Visual Studio Code: light-weight & across platforms (from code.visualstudio.com)
  - from CLI: `code <file/folder path>`
- VS Code interface
  - Language style: bottom right --> can manually override
  - Breadcrum for quick navigation: top of editing textbox
  - `Ctrl + F`: search within current file vs. "*Magdifying*" on left toolbar: search within project

### Hotkeys & shortcuts

- `F12`: function definition
  - `Ctrl + F12`: go to Implementations
  - `Shift + F12`: Go to references
  - `Shift + Alt + F12`: Find all references
- `Ctrl + Tab / Ctrl + Shift + Tab`: switch to tabs in editor ==> Hold down `Tab` to move between tabs
- `Alt + Left/Right Arrow`: Move back/forth on cursor positions history
- `Ctrl + F / Ctrl + Shift + F`: search file/folder
  - Search folder has regex for file inclusion/exclusion (e.g. auto ignore ".gitignore" files)
- `Ctrl + Shift + P`: command palette
- `Alt + Up/Down Arrow`: move lines Up/Down
- `Ctrl + /`: toggle comment
- `Alt + <MouseClick + Drag>`: Multiple cursor --> can type and replace all in one go
- `Shift + Alt + F`: format document
  - Extension "*Prettier.js*": format to selected standard

## Installing Node.js

- Use `nvm` (aka Node Version Manager) to manage different version of Node
  - Different version manager can be used. See <https://nodejs.org/en/download>

### Troubleshooting installation issues

- `JavaScript heap out of memory`: Increase Node's memory limit (see <https://stackoverflow.com/a/64409997>)
  - `export NODE_OPTIONS="--max-old-space-size=8192"`: for 16gb machine
- Windows Subsystem for Linux (WSL2): recommended as package may not be Windows compatible

## Managing packages with NPM

- Main `npm` commands:
  - `npm install`: install all project dependencies
  - `npm install/update <package>`: install/update specific package(s) --> updated only up to "wanted" version
  - `npm outdated`: which package can be upgrade
  - `npm uninstall <package>`: uninstall specific package(s)
- npm mechanism:
  - read `package.json` for list of dependencies metadata
  - download from npm registry --> saved into node_modules
  - save the actual version being used in package-lock.json: version & checksum of each pacakge
- Initialize project: create folder, then in terminal within that folder: `npm init` and `npm install`
- Add package: `npm install <package-name> --save`
  - development only package: `npm install <package-name> --save-dev` (e.g. eslint)
  - update to latest: `npm install <package-name>@latest`
- VS code integration: using NPM extension --> GUI for management
  - Use auto-generated `jsconfig.json` for extra configuration
  - Add "typeAcquisition" section to include "Node.js" --> activate code hints/annotation for Node.js variables

    ```json
    "typeAcquisition": {
      "include": ["node"]
    },
    ```

- Troubleshooting:
  - Peer dependency error: check the pkg website before use "--force" or "--legacy-peer-deps" to bypass this check, or use an older version
  - Nuclear option: delete the "node_modules" to reset --> may not work still when the cache is corrupted
    - `npm cache clean --force`: clean up local npm cache (or `npm cache verify`)

### Debugging with VS Code

- In VS Code, use the "**JavaScript Debug Terminal**" (new terminal "+" --> Select "JS debug terminal")
  - Can also open through "Run & Debug" view
  - For debug web page, create a launch.json file & start from there
- Debug Console (open via Command Palette, or "View" menu)
  - Can be used to execute snipplets on local variables
