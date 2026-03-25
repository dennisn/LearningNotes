# Introduction to AI-assisted Coding

- Pluralsight course: <https://app.pluralsight.com/ilx/video-courses/introduction-ai-assisted-coding/course-overview>

## Principles of AI-assisted Coding

- Use LLM & other AI tools to help you write, refactor, debug and document code
  - It's about augmenting your capabilities
  - Dev. still need to understand business requirements + architecture + trade-offs
- Vibe-coding: prompt -> code -> use
  - No real understanding of the code
  - No consideration of edge cases, security, maintainability
  - Great for learning & exploration, but not enough quality for production
- AI-assisted code:
  - Understand the code
  - Verify correctness, style, architectural patterns
  - Consider the bigger picture: security, technical debt, dependencies are well-maintained & trust-worthy
- AI problem:
  - Hallucinations: good at generating code that `look` good, but doesn't work
  - Technical Debt: may break the overall architectural patterns --> make it hard to maintain (fix/expand) later
  - Security vulnerabilities: inherited from its learning (e.g. Sql injection flows, hardcoded credentials, or use dependencies with vulnerabilities)

## Navigating the AI Coding Landscape

- Modern coder's tools: IDE-integrated assistants (VS Code with Copilot), AI-first IDE (Cursor, Windsurf), Web-based IDE (Bolt, Lovable), Terminal-based tools (Claude code), Chat interfaces (ChatGPT, Claude, Gemini)
- Sample workflows:
  - Bolt: initial prototype skeleton
  - VS Code + Copilot: fill out details
  - Cursor: Code refactor (*Could be done in VS Code ?*)
  - Gemini/ChatGPT: investigate bugs
  - Claude: agentic coding --> add structrued logging

  ## Goals, Tools, Reason & Tradeoff

  | Goal | Tool | Reason | Tradeoff |
  | --- | --- | --- | --- |
  | Prototype | Web-based IDE (Bolt) | Fast, no setup | Less control, harder to integrate with existing code |
  | Everyday Dev | IDE-itegrated (VS Code + Copilot) | Great for builerplat & common patterns | Re-active, not pro-active |
  | Debug/learning | Chat interface | conversations + follow up questions | context switching & manual code copy |
  | Refactoring/architectural changes | AI-first IDE (Cursor) | deeper collaboration, bigger context | New dev environment |
  | Well-defined task that touches many files | CLI tools (Claude code/agentic) | Efficient for repetitive work | Extra careful with review |
