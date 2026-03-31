# Advanced Claude Code

- Course URL: <https://app.pluralsight.com/library/courses/advanced-claude-code>
- Demo project: <https://github.com/nyisztor/novatech‑demo>

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

## Using Git Worktrees for Concurrent Workflows

## Scaling Claude Code for Teams and Enterprises

## Automating with Claude Skills

## Building Custom Hooks for Intelligent Automation
