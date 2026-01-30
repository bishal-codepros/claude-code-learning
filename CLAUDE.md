# Claude Code Learning Guide

## Project Purpose
Incremental learning guide for mastering Claude Code. No complete upfront plan - gradual rollout.

## Learning Approach
- **Style**: Hands-on, learning by doing
- **Language**: Mixed Romanized Nepali + English
- **User Role**: Driver - decides what to learn next
- **Claude Role**: Guide and helper

## Current Progress
- [x] Project setup
- [x] MCP Deep Dive - Session 1 COMPLETED
- [x] MCP Context Engineering - Session 2 COMPLETED

## MCP Learning Summary (Session 1)

### Key Learnings
1. **MCP = Tool, NOT automatic** - Claude doesn't auto-call MCP, use explicit phrases like "look up docs for..."
2. **Context7 MCP** - Already installed, use for library documentation
3. **MCP Scopes**:
   - `local` → ~/.claude.json (this project only, personal)
   - `user` → ~/.claude.json (all projects, personal)
   - `project` → .mcp.json (team shared via git)
4. **Cross-platform issue**: stdio MCPs need OS-specific commands (Windows: `cmd /c npx`, macOS: `npx`)
5. **Best practice**: HTTP MCPs in .mcp.json (project), stdio MCPs in ~/.claude.json (local)

### GitHub MCP Setup - COMPLETED ✅
- URL: `https://api.githubcopilot.com/mcp`
- Requires: Personal Access Token (classic) with `repo` scope
- Config: `.mcp.json` with `${GITHUB_PAT}` env var
- Repo: https://github.com/bishal-codepros/claude-code-learning
- Issue #1 created for next learning topics

### Chrome DevTools MCP - ADDED ✅
- Package: `chrome-devtools-mcp@latest`
- Scope: User level (~/.claude.json)
- Use: Browser control, screenshots, console, network, DOM inspect
- Status: Ready to test after restart

### MCP Commands
```bash
claude mcp add --transport http <name> <url>
claude mcp add --transport stdio <name> -- <command>
claude mcp list
claude mcp remove <name>
/mcp  # Check status inside Claude Code
```

## MCP Context Engineering (Session 2)

### Key Learnings
1. **Context Cost**: MCP tools consume ~8-17k tokens even when unused
2. **Tool Search**: `ENABLE_TOOL_SEARCH=auto:5` enables on-demand loading at 5% threshold
3. **Settings File**: `~/.claude/settings.json` for user-level env vars
4. **VSCode Limitation**: stdio MCPs may show "connected" but tools don't load in VSCode extension
5. **HTTP MCPs Work**: GitHub, Context7 (plugin) work reliably

### Context Optimization
```json
// ~/.claude/settings.json
{
  "env": {
    "ENABLE_TOOL_SEARCH": "auto:5"
  }
}
```

### Popular MCPs Discovered
| Category | MCP | Use |
|----------|-----|-----|
| Dev | Linear, Atlassian | Issue tracking |
| Monitoring | Sentry | Error tracking |
| Analytics | Amplitude | Product analytics |
| Database | PostgreSQL (dbhub) | SQL queries |
| AI/ML | Hugging Face | Models, datasets |

### Combo Workflow Tested
```
Context7 (docs) → GitHub (issue comment) ✅ Works!
```

## Next Learning Topics
- [ ] **Hooks** - Pre/post tool execution automation
- [ ] **Skills** - Custom slash commands
- [ ] **Plugins** - Extend Claude Code capabilities
- [ ] **Sub-agents** - Task delegation patterns
- [ ] **PR Review Workflow** - Code review with Claude
- [ ] **Build Custom MCP** - Create your own MCP server

## Instructions for Claude
- Help user learn Claude Code features incrementally
- Use Context7 MCP proactively for library/framework questions
- User communicates in mixed Romanized Nepali + English
- Keep responses concise
- Update docs to preserve learning progress
