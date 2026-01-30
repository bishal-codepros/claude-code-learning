# MCP Learning Guide

## What is MCP?
Model Context Protocol - A standard protocol that allows AI assistants to connect to external tools, data sources, and services.

---

## Learning Objectives

### Want to Learn
- [x] What is MCP (already known)
- [ ] How to use MCP in a project
- [ ] How to choose the correct MCP tools
- [ ] How to leverage task optimization using MCP
- [ ] When NOT to use MCP
- [ ] Popular and useful MCPs in the community
- [ ] Claude Code commands for MCP
- [ ] Secret sauce and best practices

### Already Learned
- Basic understanding of what MCP is

### Currently Learning
- Hands-on: Building a small app with simple MCP tools

---

## Hands-On Project: Simple MCP App
(To be defined - starting with a practical example)

---

## Key Concepts

### MCP Architecture
- **MCP Server**: External service that provides tools (e.g., Context7)
- **MCP Tools**: Functions exposed by the server (e.g., `resolve-library-id`, `query-docs`)
- **Protocol**: Standardized way for Claude to communicate with external services

### How Context7 MCP Works
1. **resolve-library-id** - Takes a library name → Returns Context7-compatible ID
2. **query-docs** - Takes library ID + query → Returns up-to-date documentation

### Workflow Example (FastAPI)
```
User: "How do I use FastAPI routing?"
  ↓
Claude calls: resolve-library-id("FastAPI", "routing")
  ↓
Returns: /websites/fastapi_tiangolo (12,277 snippets, score: 96.8)
  ↓
Claude calls: query-docs("/websites/fastapi_tiangolo", "routing")
  ↓
Returns: Live documentation with code examples
  ↓
Claude: Provides accurate, current answer
```

### Key Insight
- MCP fetches **real-time data**, not from Claude's training
- This means always up-to-date documentation

### Important: MCP is NOT Automatic!
Claude Code **does not automatically call MCP**. It defaults to using training data.

**To trigger MCP, use explicit phrases:**
- "Look up the docs for..."
- "Check the latest documentation..."
- "Use Context7 to find..."
- "What does the current [library] docs say about..."

**Example:**
| Prompt | Behavior |
|--------|----------|
| "Write a FastAPI app" | ❌ Uses training data only |
| "Look up FastAPI docs, then write an app" | ✅ Calls MCP first |

---

## Notes & Discoveries
- Context7 MCP is pre-configured in this environment
- Library IDs follow format: `/org/project` (e.g., `/websites/fastapi_tiangolo`)
- Higher benchmark scores = better quality documentation
- More code snippets = more comprehensive coverage

### Cross-Platform Best Practice
| File | Use For | Why |
|------|---------|-----|
| `.mcp.json` (project) | HTTP MCPs only | Cross-platform, team-shared |
| `~/.claude.json` (local) | stdio MCPs | OS-specific commands |

**Problem:** stdio MCPs need different commands per OS
- Windows: `cmd /c npx ...`
- macOS/Linux: `npx ...`

**Solution:** Keep stdio MCPs in local config, HTTP MCPs in project config.

---

## MCP Configuration

### Configuration Files
| Scope | File Location | Use Case |
|-------|---------------|----------|
| Local (default) | `~/.claude.json` | Personal, private |
| Project | `.mcp.json` (project root) | Team sharing via git |
| User | `~/.claude.json` | All your projects |

### Commands
```bash
# Add HTTP MCP (cloud services)
claude mcp add --transport http <name> <url>

# Add local stdio MCP (npm packages)
claude mcp add --transport stdio <name> -- <command> [args...]

# With scope
claude mcp add --transport http stripe --scope project https://mcp.stripe.com

# Management
claude mcp list              # List all
claude mcp get <server>      # Details
claude mcp remove <server>   # Remove
/mcp                         # Status check (inside Claude Code)
```

### .mcp.json Format
```json
{
  "mcpServers": {
    "server-name": {
      "command": "npx",
      "args": ["-y", "some-mcp-server"],
      "env": {
        "API_KEY": "${MY_API_KEY}"
      }
    }
  }
}
```

---

## GitHub MCP Setup

### Configuration (.mcp.json)
```json
{
  "mcpServers": {
    "github": {
      "type": "http",
      "url": "https://api.githubcopilot.com/mcp",
      "headers": {
        "Authorization": "Bearer ${GITHUB_PAT}"
      }
    }
  }
}
```

### Setup Steps
1. Generate GitHub PAT (classic) at https://github.com/settings/tokens
2. Select scope: `repo` (minimum required)
3. Set environment variable: `GITHUB_PAT=ghp_xxxxx`
4. Restart VSCode to pick up env var
5. Run `/mcp` to verify connection

### Secure Token Storage
- Option 1: System env var (permanent): `[System.Environment]::SetEnvironmentVariable("GITHUB_PAT", "token", "User")`
- Option 2: .env file + load script (per-session)
- Never hardcode token in .mcp.json that gets committed to git

---

## Session 1 Key Learnings

1. MCP is NOT automatic - Claude uses training data by default
2. Trigger MCP with: "look up docs for...", "check latest documentation..."
3. Context7 MCP: `resolve-library-id` → `query-docs` workflow
4. Scopes: local (personal+project), user (personal+all), project (team+git)
5. Cross-platform: HTTP MCPs in .mcp.json, stdio MCPs in ~/.claude.json
6. GitHub MCP needs PAT in Authorization header, not OAuth

---

## Resources
- MCP Docs: https://code.claude.com/docs/en/mcp
- GitHub MCP: https://github.com/github/github-mcp-server
- Context7: Already configured as plugin
