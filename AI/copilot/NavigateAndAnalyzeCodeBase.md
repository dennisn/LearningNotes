- Pluralsight course: https://app.pluralsight.com/ilx/video-courses/navigating-analyzing-codebases-github-copilot/course-overview

# Overview
- May want to create an index of the workspace first so Copilot can work with all the files
  + In VS code, Ctrl+Shift+P -> "Chat: Build Local WorkSpace Index"
- Useful prompt for project overview
  ```
  Create a project cheat sheet that contains the following:
  - Architecture overview - what are the main directories and files?
  - Key components & Patterns
  - APIs & Data Flow
  - Reuseable Utilities & Helpers - Are there existing functions you should use instead of reinventing the wheel?
  Do you see any componenets used over and over?
  ```

# Custom instruction
- Kinds of like persistent memory for copilot
  + Content is in the ".github/copilot-instructions.md" file
- Benefits:
  + Efficient codebase analysis: dont have to repeatedly analyse the codebase
  + Shared within team
  + Living documentation
  + Can include extensible content types: code standard, common pitfalls, links to external docs

# Tracing code path
- Decide starting context file with `#<filename>`
- Using the whole codebase as context with `#codebase`
- We could ask copilot to generate ASCII diagram, UML diagram (with specific dialect, such as plantUml)