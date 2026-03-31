# Claude Code in Practice

- URL: <https://app.pluralsight.com/ilx/video-courses/claude-code-practice/course-overview>

## Claude Code Modes

- Available modes: Plan, Ask and Edit --> change by Shift+Tab
  - **Plan**: for planning changes, code exploration or iterate on the development direction
  - **Ask**: Start to editing file, but always ask for permission beforehand
  - **Edit**: can edit automatically, but still ask for permissions when running bash command (e.g. create file/directory, git, etc.)

## Claude Context and End-to-End Workflows

- "**Claude.md**": provide project preference, structure, convention and workflows you expect --> always part of the context
  - `/init`: initialise the "**Claude.md**" --> equivalent to onboarding new developer to the project
  - Should add more details that may not be inferred with `/init`
- Some common *extra* sections for "**Claude.md**":
  - Workflow: how development workflow should be: new branch -> change code -> update documents -> push & PR
  - Testing: how testing is done, focus on "fix the code (not the tests, unless tests are incorrect)"
- Context limits: will trigger summarisation when getting near the limit --> may accidentally lost vital details
  - Leverage **Claude.md** for generic context
  - Break down the works to reduce context size
  - Specified files inclusion --> focus the context
  - Create specific summary, especially those relevant to tasks

## Claude Code Use Cases

- Analyse code base:
  - new codebase: to understand the architecture
  - code review: to trace the impact of changes across the system
  - debugging: to trace execution and find the bug
- Full stack feature/multi-file edits: strong point of Claude Code

## Troubleshooting and Security

- Usage limits: rolling windows/hourly
  - Use plan mode: lower token usage
  - Start fresh conversation
  - Break tasks into smaller chunks
- Truncated output: when response is too long 
  - Pompt "please continue from where you left off"
  - Ask for smaller response
- Stuck in a loop: Claude tries same failing approach repeatedly
  - Break the loop with alternative approach

### Security

- Dont share sensitive information, use environment variables instead
- Dont share trade secrets or prorietary logic
  - In `settings.json` file, can add those into "deny" permission --> not guarantee to work
- Careful with `bash` commands --> need careful check
