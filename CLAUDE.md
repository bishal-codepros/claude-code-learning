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
- [x] MCP Deep Dive - COMPLETED

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

### GitHub MCP Setup (In Progress)
- URL: `https://api.githubcopilot.com/mcp`
- Requires: Personal Access Token (classic) with `repo` scope
- Config: `.mcp.json` with `${GITHUB_PAT}` env var
- Status: Need to set GITHUB_PAT env var and restart VSCode

### MCP Commands
```bash
claude mcp add --transport http <name> <url>
claude mcp add --transport stdio <name> -- <command>
claude mcp list
claude mcp remove <name>
/mcp  # Check status inside Claude Code
```

## Instructions for Claude
- Help user learn Claude Code features incrementally
- Use Context7 MCP proactively for library/framework questions
- User communicates in mixed Romanized Nepali + English
- Keep responses concise
- Update docs to preserve learning progress
