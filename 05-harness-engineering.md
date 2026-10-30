# Harness Engineering

The Claude Code harness is the layer that controls how Claude Code behaves — what it can do, when it asks for permission, and how it integrates with your workflow.

---

## Key Files

| File | Location | Purpose |
|---|---|---|
| `settings.json` | `~/.claude/settings.json` (global) or `.claude/settings.json` (project) | Permissions, hooks, env vars, model config |
| `CLAUDE.md` | Project root or `~/.claude/CLAUDE.md` | Always-loaded instructions for Claude |
| `memory/` | `~/.claude/projects/<project>/memory/` | Persistent memory across sessions |
| `keybindings.json` | `~/.claude/keybindings.json` | Custom keyboard shortcuts |

---

## Settings.json Structure

```json
{
  "permissions": {
    "allow": [
      "Bash(git log:*)",
      "Bash(npm run test:*)",
      "Read(*)"
    ],
    "deny": [
      "Bash(rm -rf:*)"
    ]
  },
  "hooks": {
    "PreToolUse": [...],
    "PostToolUse": [...],
    "Stop": [...]
  },
  "env": {
    "MY_VAR": "value"
  }
}
```

---

## Permissions

### How They Work
- `allow` → Claude can run without prompting the user
- `deny` → Claude is blocked and must ask the user to act instead
- Default (neither) → user is prompted each time

### Permission Pattern Syntax

```
Tool(command:arguments_pattern)

Examples:
  Bash(git *:*)          — allow any git command
  Bash(npm run *:*)      — allow npm run scripts
  Read(*:*)              — allow reading any file
  Bash(rm:*)             — allow rm (be careful!)
```

### Scope
- Global settings apply everywhere
- Project settings override global for that project
- Use `/config` or `/update-config` skill to modify

---

## Hooks

Hooks are shell commands that fire on harness events. They run outside of Claude's context — the harness executes them.

| Hook | When It Fires |
|---|---|
| `PreToolUse` | Before any tool call |
| `PostToolUse` | After a tool call completes |
| `Stop` | When Claude finishes a turn |
| `Notification` | On push notification events |

### Hook Config Example

```json
"hooks": {
  "Stop": [
    {
      "matcher": "",
      "hooks": [
        {
          "type": "command",
          "command": "echo 'Claude finished' | notify-send -"
        }
      ]
    }
  ],
  "PreToolUse": [
    {
      "matcher": "Bash",
      "hooks": [
        {
          "type": "command",
          "command": "echo 'Running bash: $CLAUDE_TOOL_INPUT'"
        }
      ]
    }
  ]
}
```

---

## MCP Servers (Model Context Protocol)

MCP extends Claude Code with external tools and data sources.

### What MCP Provides
- External tool calls (databases, APIs, services)
- File system access beyond the project directory
- Custom slash commands
- Data from external systems

### Adding an MCP Server

```json
// In settings.json
{
  "mcpServers": {
    "my-server": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "/path/to/dir"],
      "env": {}
    }
  }
}
```

Or use: `claude mcp add <name> <command> [args...]`

### Common MCP Servers
- `@modelcontextprotocol/server-filesystem` — extended file access
- `@modelcontextprotocol/server-github` — GitHub API
- `@modelcontextprotocol/server-postgres` — database queries
- `@modelcontextprotocol/server-brave-search` — web search

---

## CLAUDE.md

The `CLAUDE.md` file is always injected into Claude's context. Use it for:
- Project-specific conventions ("always use snake_case")
- Commands to run tests/build
- File structure overview
- Do/don't rules for this codebase

```markdown
# Project Rules

## Commands
- Build: `npm run build`
- Test: `npm test`

## Conventions
- Use TypeScript strict mode
- No `any` types
- All API calls must have error handling
```

---

## Memory System

Claude Code has a file-based memory at `~/.claude/projects/<path>/memory/`.

| Memory Type | What to Store |
|---|---|
| `user_*.md` | User role, preferences, expertise level |
| `feedback_*.md` | Corrections and confirmed approaches |
| `project_*.md` | Goals, constraints, deadlines |
| `reference_*.md` | External system pointers |

`MEMORY.md` in that folder is the index — loaded automatically into context.

**See also:** [06-usage-optimization.md](06-usage-optimization.md) for reducing token costs from large settings.
