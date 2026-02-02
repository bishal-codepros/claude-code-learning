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
- [x] Skills & Commands - Session 3 COMPLETED
- [x] Hooks - Session 4 COMPLETED

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

## Skills & Commands (Session 3)

### Key Learnings
1. **Skills = Custom slash commands** - Invoke with `/skill-name`
2. **Auto-discovery** - Claude automatically detects skills from folders
3. **Storage locations**:
   - `~/.claude/commands/*.md` → Simple single-file commands
   - `~/.claude/skills/*/SKILL.md` → Complex skills with resources
   - Plugins → Community skills (auto-loaded)

### Skill Structure
```markdown
---
name: skill-name
description: "When to use this skill"
---
# Instructions here
```

### Skills Created
| Skill | Purpose | Location |
|-------|---------|----------|
| `thinking` | Truth-seeking Socratic response style | ~/.claude/commands/ |
| `pattern-seeker` | Identify user's subconscious patterns | ~/.claude/commands/ |

### Advanced Skills (with resources)
```
skills/my-skill/
├── SKILL.md       ← Required
├── scripts/       ← Executable code
├── references/    ← Documentation
└── assets/        ← Templates
```

### Superpowers Plugin - Key Skills
| Skill | Use When |
|-------|----------|
| `superpowers:brainstorming` | Before any new feature |
| `superpowers:systematic-debugging` | Any bug/error fix |
| `superpowers:test-driven-development` | Writing code with tests |
| `superpowers:verification-before-completion` | Before commit/PR |

## Hooks (Session 4)

### Key Learnings
1. **Hook = Automation at lifecycle points** - Run code before/after tool execution
2. **3 Hook Types**:
   - `command` → Run script (PowerShell, Bash, Node, Python)
   - `prompt` → LLM evaluates yes/no decision
   - `agent` → LLM + tools (Read, Grep) for complex verification
3. **Matcher = Tool name only** - Not arguments! Filter arguments inside script
4. **Script receives JSON stdin** - `tool_input.command`, `tool_name`, etc.
5. **Script outputs JSON stdout** - `permissionDecision: "deny"` to block

### Hook Events
| Event | When | Can Block? |
|-------|------|------------|
| `SessionStart` | Session begins | No |
| `PreToolUse` | Before tool runs | **Yes** |
| `PostToolUse` | After tool succeeds | No |
| `Stop` | Claude finishes responding | Yes (force continue) |

### Stop Hook Behavior
- Does NOT regenerate response (user already saw it)
- `{ok: false}` = Claude **continues** with reason as guidance
- `{ok: true}` = Claude **stops**

### Hooks in Skills
```yaml
---
name: my-skill
hooks:
  Stop:
    - hooks:
        - type: prompt
          prompt: "Verify quality..."
---
```
**Caution**: Multiple skill hooks can accumulate and conflict!

### Files Created
```
~/.claude/hooks/
├── block-rm-rf.ps1   ← PreToolUse: blocks rm -rf commands
└── log-bash.ps1      ← PostToolUse: logs Bash completion
```

### Configuration
```json
// ~/.claude/settings.json
{
  "hooks": {
    "PreToolUse": [{
      "matcher": "Bash",
      "hooks": [{
        "type": "command",
        "command": "powershell -File script.ps1"
      }]
    }]
  }
}
```

### Windows Issue (Known Bug)
- Superpowers plugin SessionStart hook fails
- Cause: `.sh` script + Windows backslash paths
- Status: [Issue #21468](https://github.com/anthropics/claude-code/issues/21468) open
- Workaround: Ignore error, superpowers skills still work

## Project 1: Commit Message Enforcer (Session 5)

### What We Built
- **Goal**: Block bad commit messages using hooks
- **Approach**: Prompt hook with LLM evaluation (not just regex)

### Key Insight
- Regex = structure check (format milyo ki nai)
- LLM = semantic check (meaning, quality, clarity)
- Prompt hooks use fast/cheap model - negligible overhead

### Hook Configuration
```json
{
  "type": "prompt",
  "prompt": "If this is a git commit command, evaluate the commit message. ALLOW if not a git commit. For git commits, check: (1) Uses imperative mood, (2) Descriptive, (3) Not vague, (4) Conventional commits format. DENY only if clearly low quality."
}
```

### Architecture Learning
- Matcher = tool name only (e.g., "Bash"), not command content
- Hooks run in sequence, can't conditionally skip
- Command hook blocking early saves subsequent hook calls
- Prompt filtering happens IN the prompt logic, not matcher

### Prompt Wording Insight
```
❌ "DENY only if clearly low quality"  → LLM lenient
✅ "DENY unless follows ALL rules"     → LLM strict
```
"Deny bad" vs "Allow only good" = same logic, different LLM interpretation.

### Files Created
- `~/.claude/hooks/enforce-commit-format.ps1` (PowerShell regex version - backup)

## Project 2: Visual Regression Checker (Session 5)

### What We Built
- **Goal**: Capture UI screenshots, detect visual changes, auto-create GitHub issues
- **Approach**: Chrome DevTools MCP + GitHub MCP + Custom Skill

### Workflow
```
Baseline screenshot → Code change → Current screenshot → Compare → GitHub issue
```

### Tools Used
| MCP Tool | Purpose |
|----------|---------|
| `mcp__chrome-devtools__new_page` | Open URL in browser |
| `mcp__chrome-devtools__navigate_page` | Reload with cache bypass |
| `mcp__chrome-devtools__take_screenshot` | Capture full page screenshot |
| `mcp__github__issue_write` | Create visual regression issue |

### Skill Created
- `/visual-check` - Automates the entire visual regression workflow
- Location: `~/.claude/commands/visual-check.md`

### Key Learnings
1. Chrome DevTools MCP requires Chrome with remote debugging
2. Screenshots can be saved to file path directly
3. Claude's vision can compare images for visual diffs
4. Skills can orchestrate multiple MCPs together

## Next Learning Topics (Priority Order)
1. [ ] **Sub-agents** - Task delegation patterns
2. [ ] **Plugins Deep Dive** - Create/publish plugins
3. [ ] **PR Review Workflow** - Code review with Claude
4. [ ] **Build Custom MCP** - Create your own MCP server

## Instructions for Claude
- Help user learn Claude Code features incrementally
- Use Context7 MCP proactively for library/framework questions
- User communicates in mixed Romanized Nepali + English
- Keep responses concise
- Update docs to preserve learning progress
