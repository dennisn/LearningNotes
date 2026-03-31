# Introduction to Claude Code

- Course URL: <https://app.pluralsight.com/ilx/video-courses/claude-code-introduction/course-overview>

## What is Claude Code ?

- Anthropic product, base on 3 LLM models: Haiku (fast), Sonnet (balance) and Opus (most intelligent)
- Characteristics
  - Generated code directly into files & directories (no need for copy & paste)
  - From prompt, self-organize into multiple steps --> agentic work
  - Can use tools to perform actions
- Summary: `an agentic AI coding partner that can use tools to complete many actions without constant direct user input`
- Some useful commands: `/login`, `/status`, `/usage`, `/doctor` (for troubleshooting)

## Using Claude Code

- Tools: very useful, built-in set already cover a lot, but can add more customised tools
- Claude's permission model is read only by default, but can set to be permissible for the rest of session
- Memory (visible with `/context`): include system prompts, tools, and messages
  - When memory is 95% full, will autocompact the buffer
  - Can export the messages section with `/export`
  - Could empty the context with `/clear`
  - Best practice: run `/init` on new project to create "*CLAUDE.md*", which is the context overview of the project for claude
- Skills: custom rules that get included as context **only** when they're called
- Hooks: user-defined shell commands that execute at various points in Claude Code's lifecycle, providing deterministic control over Claude Code's behavior
  - Common: `PreToolUse` & `PostToolUse`
- Alternatives:
  - Agentic IDE: Copilot with VS Code, Cursor, Windsurf, Antigravity
  - Terminal UI (TUI): OpenAI Codex CLI, Google Gemini CLI, Aider & OpenCode
