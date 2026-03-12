# Building a JavaScript Development Environment
  - Pluralsight course: https://app.pluralsight.com/ilx/video-courses/javascript-building-development-environment/course-overview

# Integrated Development Environment (IDE)
  - IDE: editor with "*intrgrated*" tools for development works (e.g. build, debug, syntax highlight)
    + Contextual help (e.g. auto-complated)
    + File management & source-code control
    + Debug capabilities
    + Customizable working environment (e.g. hotkeys, layout)
    + Add functionalities with extensions
  - Visual Studio Code: light-weight & across platforms (from code.visualstudio.com)
    + from CLI: `code <file/folder path>`
  - VS Code interface
    + Language style: bottom right --> can manually override
    + Breadcrum for quick navigation: top of editing textbox
    + `Ctrl + F`: search within current file vs. "*Magdifying*" on left toolbar: search within project
## Hotkeys & shortcuts
  - `F12`: function definition
    + `Ctrl + F12`: go to Implementations
    + `Shift + F12`: Go to references
    + `Shift + Alt + F12`: Find all references
  - `Ctrl + Tab / Ctrl + Shift + Tab`: switch to tabs in editor ==> Hold down `Tab` to move between tabs
  - `Alt + Left/Right Arrow`: Move back/forth on cursor positions history
  - `Ctrl + F / Ctrl + Shift + F`: search file/folder
    + Search folder has regex for file inclusion/exclusion (e.g. auto ignore ".gitignore"  files)
  - `Ctrl + Shift + P`: command palette
  - `Alt + Up/Down Arrow`: move lines Up/Down
  - `Ctrl + /`: toggle comment
  - `Alt + <MouseClick + Drag>`: Multiple cursor --> can type and replace all in one go
  - `Shift + Alt + F`: format document
    + Extension "*Prettier.js*": format to selected standard

# Installing Node.js
  - Use `nvm` (aka Node Version Manager) to manage different version of Node
    + Different version manager can be used. See https://nodejs.org/en/download

## Troubleshooting installation issues
  - `JavaScript heap out of memory`: Increase Node's memory limit (see https://stackoverflow.com/a/64409997)
    + `export NODE_OPTIONS="--max-old-space-size=8192"`: for 16gb machine
  - Windows Subsystem for Linux (WSL2): recommended as package may not be Windows compatible

# Managing packages with NPM

## Debugging with VS Code