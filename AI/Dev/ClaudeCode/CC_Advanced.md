# Advanced Claude Code

- Course URL: <https://app.pluralsight.com/library/courses/advanced-claude-code>
- Demo project: <https://github.com/nyisztor/novatech‑demo>

- Official documentation: <https://code.claude.com/docs/>
- Claude code source code: <https://github.com/anthropics/claude-code>

## Connecting Claude Code to External Tools

- MCP configuration scope
  - Local: project-specific setting for local use only, not share --> for testing
  - Project: add to `.mcp.json` in project root --> team-shared, version-controlled
  - User: add to `~/.claude.json` --> accessible to all user projects
- MCP servers enforce token limits to protect context window
  - May override with `MAX_MCP_OUTPUT_TOKENS` to avoid warnings/errors like "output limit exceeded"

### Github MCP

- Generate access token from github: token can be for specific repository, with limited permissions
- Set token as environment variable: `setx GITHUB_TOKEN "your_token_here"`, or `export GITHUB_TOKEN=your_token_here` for Linux (may put into "~/.bashrc" file)
- Add github MCP to project

  ```shell
  claude mcp add --scope project github --env GITHUB_PERSONAL_ACCESS_TOKEN=${GITHUB_TOKEN} \
  -- npx -y @modelcontextprotocal/server-github
  # In windows, should use "cmd /c npx -y @modelcontextprotocal/server-github" instead to avoild "Connection Closed" error
  ```

- Equivalent adding this to configuration file `.mcp.json` in project root

  ```json
  {
      {
          "mcpServers": {
              "github": {
                  "type": "stdio",
                  "command": "npx",
                  "args": ["-y", "@modelcontextprotocol/server-github"],
                  "env": {
                      "GITHUB_PERSONAL_ACCESS_TOKEN": "${GITHUB_TOKEN}"
                  }
              }
          }
      }
  }
  ```

### Figma MCP

- Connect through Figma Desktop with Developer mode on (Need Developer/Pro plan)
- Allow Claude "*see*" the current selection on Figma Desktop

## Working with Subagents for Parallel Development

- Subagents: task-specific AI assistants that Claude code/main agent can delegate task to
  - Preseve main context, as subagent run in separated context
  - If description include "PROACTIVELY" or "MUST BE USED" --> nudge Claude to use the sub-agent when tasks matched
  - Can explicitly request a subagent, or prevent the use of subagent in prompt
- Subagent structure:
  - Meta data: name, description, tools and model --> define when it should be used, which tools it could access and which model to run
  - Body: responsibility, tasks, etc. --> scope the agent expertise and keep it focus
- Async Subagent: runs in the background & reconnects to main agents with its result
  - Activate with `Ctrl+B`, or prompt it to run in the background
  - Only for long-running, **isolated** tasks
- Built-in subagents
  - `general-purpose`: full tools, for complex, multi-step tasks
  - `explore`: fast, for codebase searches with multiple throughness levels
  - `plan`: gathers context to propose changes
- Best practices
  - Subagent for repetitive, specialized tasks
  - Provide suitable tool access
  - Use descriptive names & descriptions --> help Claude know when to invoke
  - Consider background execution for long running tasks --> mutliple async sub-agents in parallel
  - Commit project sub-agents --> shared consistent workflows with team

## Using Git Worktrees for Concurrent Workflows

- Git worktree: multiple branches checked out simultaneously in separate directories
  - Support simulteneous agent context --> fixing of urgent work, while keep current workflow
  - Each worktree has its own file state, but share same git history --> parallel workspaces
  - Each branch can only be checked out in one worktree at a time

```shell
# View worktree list
git worktree list

# add worktree
git worktree add ../<new_worktree_folder> -b <new_branch_name>

# when finishing (committed & pushed), can remove worktree
git worktree remove ../<worktree_folder>

# clean up any stale worktree references (i.e. after manually delete the worktree branch)
git worktree prune
```

- Claude desktop can automatically create worktree for each session, normally under `~/.claude-worktrees`
  - files in `.gitignore` won't be copied --> create `.worktreeinclude` in repository root

## Scaling Claude Code for Teams and Enterprises

- `Claude.md` file for organisation-wide could be set up by administrator
  - For complex rule, can split into separate files and use `@import` syntax to reference
  - In project, all `*.md` files in ".claude/rules" directory are automatically loaded with `CLAUDE.md`
  - Using YAML frontmatter to scope rules to specific file types

    ```YAML
    ---
    paths:
      - "src/api/**/*.ts"
    ---
    ```

- Usage:
  - Pro/Max: flat rate so no tool
  - API: `/cost` + desktop dashboard
  - 3rd party cloud: through OpenTelemetry

### Authentication

- Claude Console
  - for teams using Anthropic API --> member are invited & assigned roles
  - Built-in usage tracking & analytics
- Claude Pro/Max
  - Simpler setup --> lack usage analytics
- Amazon Bedrock/Google Vertex/ Microsoft Foundry
  - all requests stay within security boundary
- Claude for Teams & Enterprise
  - Add extra organisational capabilities

### Permissions

- Tool categories
  - Read-only tools: no approval needed
  - Bash commands: approval once per project --> persist across sessions
  - File modifications: approval needed for every session
- Permissions settings
  - `~/.claude/settings.json`: user
  - `.claude/settings.json`: shared within project
  - `.claude/settings.local.json`: personal override per project
- Permission levels: **ALLOW**, **ASK**, **DENY**
  - Can view with `/permissions` command
- Settings precedence: Enterprise managed policies > CLI > Local project > Shared project > User
  - Enterprise managed policies are system-wide, manged by administrator

Sample:

```json
{
  "permissions": {
    "allow": [
      "Bash(npm test)",
      "Bash(npm run lint)",
      "Bash(git diff:*)"
    ],
    "deny": [
      "Bash(rm -rf:*)",
      "Bash(curl:*)",
      "Edit(*.env)"
    ]
  }
}
```

## Automating with Claude Skills

- Skills are reusable instructions
- Skill basic structure
  - Folder with the skill name
  - `SKILL.MD` file:
    - YAML frontmatter: name + description
      - `allowed-tools`
      - `user-invocable`: hides the skill from the slash menu, but Claude can still use it
      - `disable-model-invocation`: block automatic/programmatic invocation
    - Mark-down instruction as the body
- Best practice:
  - Keep `SKILL.MD` under 500 lines for best performance
    - Put essential information in `SKILL.MD` file, but details instruction in referenced separated file --> Claude only read the referenced file when needed
    - Run premade scripts without reading it --> reduce context, ensure consistent
  - Tool restrictions: reduce chance of skills doing unexpected action

## Building Custom Hooks for Intelligent Automation

- Hook: user-defined shell commands that "**deterministically**" execute automatically at specific points in Claude Code's lifecycle
  - hooks are defined in `settings.json` file
  - can be add/edit/remove interactively in Claude session
- Example:
  - Automatic formatting: run after every edit
  - Logging & compliance: track every command Claude executes --> audit/debug
  - Pre-flight validation: check that required files exisit, build scripts haven't been modified
  - Quality gates: trigger linters, lightweight test tweets, or static analysis tools --> stop if anything fails
  - Workflow coordination: update status, tagging commit, generate short summaries after completion
- Common hooks:
  - SessionStart: good for initialization/logging
  - UserpromptSubmit: for validate prompts, block certain types of requests
  - PermissionRequest: for allow/deny the request automatically
  - PreToolUse: if return exit code 2, Claude will not run the tool --> can explain why the operation is blocked
  - Notification: when Claude need human input/permission to proceed
  - PostToolUse: best for formatters, linters, or post processing on changes
  - Stop, SubagentStop: for enforce conditions, or force Claude to continue if something isn't complete
  - SessionEnd: clean up/tear down or final logging
- Hooks can also be defined for skills & sub-agent in their frontmatter
  - Scope to the skill/sub-agent only --> not clutter other works

### Best practice

- Security consideration
  - Hook run with your credentials: full access to files & commands --> dangerous
    - Review hook code before registering
    - Avoid hooks from untrusted sources
  - Validate & sanitize inputs
  - Quote shell variables: use `"$var"`, not `$var`
  - Block path traversal (e.g. check for ".." in file paths)
  - Use absolute path: reference scripts with `$CLAUDE_PROJECT_DIR`
  - Avoid processing .env, .git/, keys
- Testing hook safely
  - Test command manually first
  - start with logging
  - Use it with `.claude/settings.local.json` (i.e. personal setting) first, before moving to shared settings
  - Lower the timeout to prevent runaway hooks (default timeout=60 seconds)
- Multiple hooks on same event ==> run in parallel
  - To run sequentially, combined them into a script
- When share hook
  - Share in project settings: `.claude/settings.json`
  - Add comment in scripts
  - Create hook document in `.claude/hooks/README.md`
  - Review hook edits
