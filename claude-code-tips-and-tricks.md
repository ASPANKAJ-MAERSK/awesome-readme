# Claude Code — Ultimate Tips & Tricks Guide

> Distilled from [FlorianBruniaux/claude-code-ultimate-guide](https://github.com/FlorianBruniaux/claude-code-ultimate-guide) — 6 months of daily practice, 24K+ lines of research.

---

## Table of Contents

1. [Getting Started](#1-getting-started)
2. [Essential Commands](#2-essential-commands)
3. [Keyboard Shortcuts & Quick Actions](#3-keyboard-shortcuts--quick-actions)
4. [Context Management (Critical!)](#4-context-management-critical)
5. [Memory & Settings (CLAUDE.md)](#5-memory--settings-claudemd)
6. [Permission Modes](#6-permission-modes)
7. [Plan Mode & Thinking](#7-plan-mode--thinking)
8. [Model Selection & Cost Optimization](#8-model-selection--cost-optimization)
9. [Prompting Best Practices](#9-prompting-best-practices)
10. [Agents](#10-agents)
11. [Skills](#11-skills)
12. [Custom Commands](#12-custom-commands)
13. [Hooks (Automation)](#13-hooks-automation)
14. [MCP Servers](#14-mcp-servers)
15. [Session Management](#15-session-management)
16. [Advanced Patterns](#16-advanced-patterns)
17. [Working with Images & Visuals](#17-working-with-images--visuals)
18. [CLI Flags Reference](#18-cli-flags-reference)
19. [CI/CD & Headless Mode](#19-cicd--headless-mode)
20. [Security Tips](#20-security-tips)
21. [Community Tools](#21-community-tools)
22. [The Golden Rules](#22-the-golden-rules)
23. [Common Issues Quick Fix](#23-common-issues-quick-fix)

---

## 1. Getting Started

### Installation

```bash
# Universal (all platforms)
npm install -g @anthropic-ai/claude-code

# macOS (Homebrew)
brew install claude-code

# macOS / Linux (Shell Script)
curl -fsSL https://claude.ai/install.sh | sh

# Windows (PowerShell)
irm https://claude.ai/install.ps1 | iex
```

### Verify & Update

```bash
claude --version       # Check version
claude update          # Update to latest
claude doctor          # Health check
```

### First Launch

```bash
cd your-project
claude
```

On first launch: authenticate with your Anthropic account → accept ToS → Claude indexes your project.

### The Core Workflow

```
Describe → Claude Analyzes → Review Diff → Accept (y) / Reject (n) → Verify
```

> 💡 **Critical**: Always read the diff before accepting. It's your safety net.

---

## 2. Essential Commands

| Command | Action | When to Use |
|---------|--------|-------------|
| `/help` | Show all commands | When you're lost |
| `/clear` | Clear conversation | Start fresh |
| `/compact` | Summarize context | Context >70% |
| `/status` | Show session info + context usage | Check context |
| `/plan` | Enter Plan Mode (safe, read-only) | Complex/risky tasks |
| `/rewind` | Undo changes | Made a mistake |
| `/exit` or `Ctrl+D` | Exit Claude Code | Done working |
| `/voice` | Toggle voice input | Speak instead of type |
| `/mcp` | Check MCP server status | Debug MCP issues |
| `/model` | Change model mid-session | Switch Sonnet ↔ Opus |
| `/rename` | Rename the session | Running parallel sessions |

---

## 3. Keyboard Shortcuts & Quick Actions

| Shortcut | Action |
|----------|--------|
| `Shift+Tab` | Toggle between permission modes |
| `Shift+Tab × 2` | Enter Plan Mode |
| `Alt+T` | Toggle thinking on/off (faster & cheaper for simple tasks) |
| `Ctrl+C` | Cancel current operation |
| `Ctrl+D` | Exit Claude Code |

### Shell Quick Actions

| Syntax | What It Does | Example |
|--------|-------------|---------|
| `!command` | Run shell command directly | `!git status`, `!npm test` |
| `@file.ts` | Reference a specific file | `Fix the bug in @src/auth/login.tsx` |

---

## 4. Context Management (Critical!)

### Context Status Line

```
Model: Sonnet | Ctx: 89.5k | Cost: $2.11 | Ctx(u): 56.0%
```

Watch `Ctx(u):` — act before it's too late.

### Thresholds

| Context % | Status | Action |
|-----------|--------|--------|
| 0–50% | 🟢 Green | Work freely |
| 50–70% | 🟡 Yellow | Be selective with new files |
| 70–90% | 🟠 Orange | **`/compact` now** |
| 90%+ | 🔴 Red | **`/clear` required** |

### Actions by Symptom

| Sign | Action |
|------|--------|
| Short / repetitive responses | `/compact` |
| Claude forgetting earlier context | `/clear` |
| Context >70% | `/compact` |
| Task complete, switching topics | `/clear` |

### Context Recovery Commands

| Command | Usage |
|---------|-------|
| `/compact` | Summarize conversation and free up context |
| `/clear` | Fresh start (loses history) |
| `/rewind` | Undo recent changes |
| `claude -c` | Resume last session (continues context) |
| `claude -r <id>` | Resume specific session by ID |

> 💡 **Pro tip**: Use `claude -c` as your default startup in active projects — never lose context from the previous session unless you want a fresh start.

---

## 5. Memory & Settings (CLAUDE.md)

### Memory Hierarchy

```
~/.claude/CLAUDE.md          → Global (applies to ALL projects)
/project/CLAUDE.md           → Project (committed to git, shared with team)
/project/.claude/CLAUDE.md   → Personal local (gitignored, your overrides)
```

**Priority**: Project overrides Personal; local settings override global.

### .claude/ Folder Structure

```
.claude/
├── CLAUDE.md            # Local memory (gitignored)
├── settings.json        # Hooks, team settings (committed)
├── settings.local.json  # Your permission overrides (not committed)
├── agents/              # Custom agent definitions
├── commands/            # Slash command definitions
├── hooks/               # Event-driven scripts
├── rules/               # Auto-loaded rules
└── skills/              # Reusable knowledge modules
```

### CLAUDE.md Best Practices

Put in CLAUDE.md at project root:
- Project purpose and architecture overview
- Coding conventions (naming, formatting, testing standards)
- Prohibited patterns ("never use `any` in TypeScript")
- Preferred libraries and stack
- Common workflows and how to run tests

### Settings Files

| File | Where | Committed? | Purpose |
|------|-------|-----------|---------|
| `CLAUDE.md` | Project root | ✅ Yes | Team instructions |
| `settings.json` | `.claude/` | ✅ Yes | Team hooks & settings |
| `settings.local.json` | `.claude/` | ❌ No | Your local permission overrides |
| `CLAUDE.md` | `~/.claude/` | ❌ No | Personal global preferences |

---

## 6. Permission Modes

| Mode | File Editing | Code Execution |
|------|-------------|----------------|
| **Default** | Asks each time | Asks each time |
| **acceptEdits** | Auto-accepts | Asks each time |
| **Plan Mode** | ❌ None | ❌ None |
| **dontAsk** | Only if in allow-list | Only if in allow-list |
| **bypassPermissions** | Auto | Auto (CI/CD only!) |

Switch modes with **`Shift+Tab`**.

---

## 7. Plan Mode & Thinking

### Activating Plan Mode

- `Shift+Tab × 2` in the terminal
- `/plan` command
- `--permission-mode plan` CLI flag

Plan Mode = safe read-only exploration. Claude reads and analyzes but makes **no changes**.

### OpusPlan Mode

```bash
/model opusplan
```

Uses **Opus** for deep planning, then switches to **Sonnet** for execution — best quality/cost balance for complex tasks.

**Workflow**: `/model opusplan` → `Shift+Tab × 2` (plan with Opus) → `Shift+Tab` (execute with Sonnet)

### Ultraplan (v2.1.91+)

```bash
/ultraplan <task description>
```

Sends planning to the cloud in the browser while your terminal stays free. Review the plan in-browser, then approve for execution.

### Thinking / Effort Levels

| Control | Action | Persistence |
|---------|--------|-------------|
| `Alt+T` | Toggle thinking on/off | Session only |
| `/config` | Enable/disable globally | Permanent |
| `/model` slider (← →) | `low | medium | high | xhigh` | Session |
| `CLAUDE_CODE_EFFORT_LEVEL` env var | `low|medium|high|xhigh|max` | Shell session |
| `effortLevel` in settings.json | same values | Permanent |

> 💡 **Cost tip**: Press `Alt+T` to disable thinking for simple tasks → faster and cheaper.

> 💡 **Opus 4.7**: Default effort in Claude Code = `xhigh`. Use `ultrathink` in your prompt to force max effort for the next turn.

**Required for**: features touching >3 files, architecture decisions, complex debugging.

---

## 8. Model Selection & Cost Optimization

### Quick Model Decision

| Task Type | Model | Effort |
|-----------|-------|--------|
| Rename, boilerplate, test gen | **Haiku** | low |
| Feature dev, debug, refactor | **Sonnet** | medium–high |
| Architecture, security audit | **Opus** | high–max |

### Cost Reference (approximate)

| Model | Input | Output | Best For |
|-------|-------|--------|----------|
| Opus 4.7 | $5/MTok | $25/MTok | Complex reasoning (10–20% of tasks) |
| Sonnet 4.6 | $3/MTok | $15/MTok | Most development (70–80% of tasks) |
| Haiku 4.5 | $0.80/MTok | $4/MTok | Simple fixes, validation (5–10% of tasks) |

### Dynamic Switching Mid-Session

```bash
# Complex feature encountered
/model opus        # Switch to deep reasoning

# Back to routine work
/model sonnet      # Speed + cost optimization
```

> ✅ Swap **on task boundaries**, not mid-task.
> ❌ Don't swap mid-implementation (context confusion).

### Cost Optimization Tips

- Use `--model haiku` for CI linting/simple checks
- Use `OpusPlan` to plan with Opus and execute with Sonnet
- Disable thinking (`Alt+T`) for mechanical tasks
- Set per-skill `effort: low` for commit/sync/scaffold skills

---

## 9. Prompting Best Practices

### The WHAT/WHERE/HOW/VERIFY Formula

```
WHAT:   [Concrete deliverable]
WHERE:  [File paths]
HOW:    [Constraints, approach]
VERIFY: [Success criteria]
```

**Example:**
```
Add input validation to the login form.
WHERE: src/components/LoginForm.tsx
HOW: Use Zod schema, show inline errors below each field
VERIFY: Empty email shows error, invalid format shows error, valid input submits successfully
```

### Anti-Patterns vs. Best Practices

| ❌ Don't | ✅ Do |
|----------|-------|
| Give vague prompts | Specify file + line with `@references` |
| Accept without reading | Read every diff before accepting |
| Ignore context warnings | Use `/compact` at 70% |
| Use negative constraints only | Provide alternatives, not just "don't do X" |
| Describe UI in words | Paste a screenshot or mockup image |

### Structured Prompting with XML Tags

For complex instructions, XML tags prevent misinterpretation:

```
<task>Refactor the auth module</task>
<constraints>Preserve existing API contracts, no new dependencies</constraints>
<output>List of changed files + summary</output>
```

### Quick Decision Tree

```
Simple task       → Just ask Claude directly
Complex task      → Plan Mode first, then execute
Risky change      → Plan Mode + review diff carefully
Repeating task    → Extract to agent, skill, or command
Context full      → /compact or /clear
Need library docs → Use Context7 MCP
Deep analysis     → Switch to Opus with thinking on
```

---

## 10. Agents

### What Are Agents?

Specialized AI personas stored as markdown files. Each agent has a specific role, allowed tools, and model.

### Creating an Agent

Save as `.claude/agents/my-agent.md`:

```yaml
---
name: my-agent
description: Use when [trigger condition]
model: sonnet
tools: Read, Write, Edit, Bash
---

# Agent Name

## Role
You are a [specialist role]. You [core responsibility].

## Instructions
[Step-by-step instructions]

## Rules
- Always [rule 1]
- Never [rule 2]
```

### Agent Best Practices

- One agent per domain/concern (e.g., `security-reviewer`, `test-writer`, `db-migrator`)
- Write trigger descriptions precisely — Claude uses them to decide when to invoke
- Restrict tools to only what the agent needs
- Use lower-cost models (Haiku) for mechanical agents (commit, scaffold)
- Use higher models (Opus) for analytical agents (security, architecture)

### Per-Agent Effort Control

Add `effort: low` or `effort: high` in agent frontmatter to override session-level effort automatically.

---

## 11. Skills

### What Are Skills?

Reusable knowledge modules (markdown files) that inject context into conversations. Unlike agents (personas), skills are topic-specific knowledge blobs.

### Creating a Skill

Save as `.claude/skills/my-skill.md`:

```yaml
---
name: my-skill
description: Knowledge about [topic]
effort: medium
---

# Skill Name

## Knowledge
[Structured knowledge, patterns, examples]
```

### The 20% Rule (When to Create a Skill)

| Frequency (across sessions) | Recommended Action |
|-----------------------------|--------------------|
| >20% of sessions | Add to `CLAUDE.md` (always-load rule) |
| 5–20% of sessions | Create a skill (load on demand) |
| <5% of sessions | Create a slash command (explicit invocation) |

---

## 12. Custom Commands

### What Are Commands?

Slash commands stored as markdown files. Invoked explicitly with `/command-name [args]`.

### Creating a Command

Save as `.claude/commands/my-command.md`:

```markdown
---
description: Brief description of what this does
argument-hint: "<required_arg> [--flag]"
---

# Command Name

Instructions for Claude when this command is invoked.
Use $ARGUMENTS[0], $ARGUMENTS[1] (or $0, $1) for user-provided args.
```

---

## 13. Hooks (Automation)

### What Are Hooks?

Shell scripts triggered automatically by Claude Code events — no manual invocation needed.

### Event Types

| Event | When It Fires |
|-------|--------------|
| `PreToolUse` | Before Claude runs any tool |
| `PostToolUse` | After Claude runs any tool |
| `Notification` | On Claude notifications |
| `Stop` | When a session ends |

### Hook Exit Codes

| Exit Code | Meaning |
|-----------|---------|
| `0` | Continue normally |
| `2` | Block the action (prevent the tool from running) |

### Bash Hook Template (macOS/Linux)

```bash
#!/bin/bash
INPUT=$(cat)    # Receives JSON from Claude Code
# Add your logic here
exit 0          # 0=continue, 2=block
```

### PowerShell Hook Template (Windows)

```powershell
$input = [Console]::In.ReadToEnd() | ConvertFrom-Json
# Add your logic here
exit 0  # 0=continue, 2=block
```

### Security Hook Ideas

- Block `rm -rf` or destructive shell commands
- Require confirmation before production deployments
- Auto-run linter after every file edit
- Log all Bash commands to an audit file
- Prevent committing secrets (scan for `API_KEY`, `password =`)

---

## 14. MCP Servers

### What is MCP?

Model Context Protocol — a standard for giving Claude access to external tools and data sources.

### Essential MCP Servers

| Server | Purpose | Install |
|--------|---------|---------|
| **Serena** | Codebase indexing, symbol search, session memory | `uvx serena` |
| **Context7** | Up-to-date library documentation | `npx context7` |
| **Playwright** | Browser automation & testing | `npx @playwright/mcp` |
| **Postgres** | Direct database queries | `npx @modelcontextprotocol/server-postgres` |
| **Sequential Thinking** | Structured reasoning chains | `npx @modelcontextprotocol/server-sequential-thinking` |
| **grepai** | Semantic code search + call graphs | `npx grepai` |
| **Figma** | Design file access (official) | `https://mcp.figma.com/mcp` |

### Adding an MCP Server

```json
// ~/.claude.json or .mcp.json
{
  "mcpServers": {
    "context7": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@upstash/context7-mcp"]
    }
  }
}
```

### MCP Debug Commands

```bash
claude mcp list          # List configured servers
/mcp                     # Check status inside Claude session
claude --mcp-debug       # Debug MCP connections
```

### Serena Quick Reference

```bash
# Index your project (run once)
uvx --from git+https://github.com/oraios/serena serena project index

# Force full rebuild
serena project index --force-full

# Incremental update (faster)
serena project index --incremental --parallel 4
```

Serena memory operations (inside Claude):
- `write_memory()` — save important context
- `read_memory()` — retrieve saved context
- `list_memories()` — see all stored memories

---

## 15. Session Management

### Resuming Sessions

```bash
claude -c               # Continue last session
claude -r <session-id>  # Resume specific session
claude --resume         # Interactive session picker
claude --from-pr 123    # Resume session linked to PR #123
```

### When to Resume vs. Start Fresh

| Resume When... | Start Fresh When... |
|----------------|---------------------|
| Continuing the same feature/task | Switching to unrelated work |
| Context is still relevant (<75%) | Previous session went off track |
| Multi-step implementation in progress | Context is bloated (>85%) |
| Follow-up on code review | Quick one-off question |

### Session Auto-Rename Tip

Running multiple parallel sessions? Add to `~/.claude/CLAUDE.md`:

```markdown
## Session Naming
Once the session's main subject is clear (after 2-3 exchanges),
run `/rename` with a short, descriptive name.
```

This makes `/resume` show meaningful names instead of timestamps.

### Desktop App vs CLI

| Use Desktop when... | Use CLI when... |
|--------------------|-----------------|
| You want visual diff review | You need scripting or automation |
| Onboarding colleagues | You use third-party providers (Bedrock, Vertex) |
| You want session sidebar | You need multi-agent orchestration |
| File attachments (images, PDFs) | You're on Linux |

---

## 16. Advanced Patterns

### The Trinity Pattern

The core pattern for agentic work: **CLAUDE.md + Agents + Hooks**

1. `CLAUDE.md` — persistent project context and rules
2. Agents — specialized personas for different concerns
3. Hooks — automated guardrails and side effects

### Composition Patterns

- **Serial**: Agent A → Agent B → Agent C (pipeline)
- **Parallel**: Run multiple agents on different workstreams simultaneously (git worktrees)
- **Hierarchical**: Orchestrator agent delegates to specialist sub-agents

### Git Best Practices with Claude Code

```bash
# Let Claude commit frequently after each completed task
# Keep commits atomic and conventional:
"feat: add JWT refresh token support"
"fix: handle plus signs in email validation"
"test: add auth integration tests"
```

- Commit after each logical unit of work
- Use `--worktree` / `-w` for isolated parallel tasks
- Review git diff before every commit

### Tight Feedback Loop

```bash
# Pattern: Describe → Implement → Test → Commit → Repeat
You: Add input validation
Claude: [Implements]
You: !npm test
Claude: [Sees results, fixes failures]
You: Commit this
```

### Todo as Instruction Mirrors

Use `TodoWrite` (or the Tasks API) to give Claude a visible task list:

```
You: Create a task list for the auth system feature
Claude: [Creates structured task list with dependencies]
You: TaskList         ← See current state
You: Work on Task 1
```

### Batch Operations Pattern

For repetitive work across many files:

```
You: Rename all occurrences of "userId" to "userID" across the entire codebase
```

Claude uses grep + multi-file edit tools — faster and more consistent than manual work.

### Vibe Coding (Skeleton Projects)

For greenfield work:

1. Describe the app concept
2. Ask Claude for a skeleton structure
3. Review and approve the scaffold
4. Iteratively add features

```
You: Create a skeleton for a REST API with Express, Prisma, and TypeScript.
     Include auth, CRUD for users, and a health endpoint.
```

### Multi-Instance Workflows

Run separate Claude instances for different concerns simultaneously:

```bash
# Terminal 1 — feature development
claude --worktree

# Terminal 2 — writing tests
claude --worktree

# Terminal 3 — code review
claude -p "Review the changes in branch feature/auth" --output-format json
```

### Session Teleportation

Move a CLI session to the Desktop app (macOS/Windows):

```bash
/desktop    # From inside an active CLI session
```

Or start in the browser and bring back to terminal:

```bash
claude --teleport
```

### Remote Control (Mobile Access, Pro/Max only)

```bash
# Start from terminal
claude remote-control

# Or from inside Claude
/rc
```

Then scan the QR code with your phone to control the session remotely.

---

## 17. Working with Images & Visuals

### How to Pass Images to Claude

1. **Paste from clipboard**: Copy screenshot (`Cmd+Shift+4` / `Win+Shift+S`), then `Cmd+V`/`Ctrl+V` in the Claude session
2. **Drag & drop** image file into terminal
3. **Reference by path**: `Analyze this mockup: /path/to/design.png`

### Use Cases

```bash
# Implement UI from mockup
[Paste Figma screenshot]
"Implement this login screen in React with Tailwind CSS"

# Debug visual bug
[Paste broken layout screenshot]
"The submit button is misaligned. Fix the CSS."

# Convert whiteboard diagram to code
[Paste photo of whiteboard]
"Convert this algorithm to Python"
```

### Image Optimization

| Image Size | Tokens Used |
|------------|-------------|
| 200×200 | ~54 tokens |
| 500×500 | ~334 tokens |
| 1000×1000 | ~1,334 tokens |
| 1568×1568 | ~3,279 tokens |

**Best format**: PNG for wireframes/diagrams; WebP for screenshots.
**Optimal size**: 1000–1200px on longest side.

> 💡 Use Figma MCP for structured data instead of screenshots — uses 3–10× fewer tokens.

---

## 18. CLI Flags Reference

| Flag | Usage |
|------|-------|
| `-p "query"` | Non-interactive / headless mode |
| `-c` / `--continue` | Continue last session |
| `-r <id>` / `--resume <id>` | Resume specific session |
| `--from-pr <number>` | Link session to GitHub PR |
| `--teleport` | Teleport session from web |
| `--model sonnet` | Set model for session |
| `--add-dir ../lib` | Allow access to directory outside CWD |
| `--permission-mode plan` | Start in Plan Mode |
| `--tools "Tool1,Tool2"` | Restrict to specific tools |
| `--allowedTools "Edit,Read"` | Whitelist tools |
| `--max-budget-usd 5.00` | Cap API spend (headless mode) |
| `--system-prompt "..."` | Append custom system prompt |
| `-w` / `--worktree` | Run in isolated git worktree |
| `--dangerously-skip-permissions` | Auto-accept all (CI only!) |
| `--output-format json` | JSON output for scripting |
| `--debug` | Verbose debug output |
| `--mcp-debug` | Debug MCP connections |

### Key CLI Subcommands

| Command | Description |
|---------|-------------|
| `claude update` | Check and install updates |
| `claude doctor` | Diagnose health issues |
| `claude mcp list` | List MCP servers |
| `claude auth login` | Authenticate (CLI / CI) |
| `claude project purge [path]` | Delete all Claude state for a project |
| `claude ultrareview [target]` | Non-interactive cloud code review for CI |
| `claude plugin details <name>` | Show plugin info and token cost |
| `claude remote-control` | Start remote control session |

---

## 19. CI/CD & Headless Mode

```bash
# Non-interactive execution
claude -p "analyze this file for security issues" src/api.ts

# JSON output for scripting
claude -p "review PR changes" --output-format json

# Economic model for CI linting
claude -p "run lint checks" --model haiku

# With auto-accept (CI only — use carefully!)
claude -p "fix all TypeScript errors" --dangerously-skip-permissions

# Cap spend to avoid runaway costs
claude -p "generate tests" --max-budget-usd 2.00
```

> ⚠️ Never use `--dangerously-skip-permissions` outside CI/CD pipelines.

---

## 20. Security Tips

### Essential Security Practices

1. **Always read diffs** before accepting — especially for unfamiliar code paths
2. **Use Plan Mode** (`/plan`) for any risky or destructive operations
3. **Never use `bypassPermissions`** outside automated CI
4. **Review MCP server sources** — malicious MCP servers exist; only use trusted sources
5. **Audit hooks** — hooks can run arbitrary shell commands; treat them like production code

### Security Hooks to Add

```bash
# Block destructive commands
if echo "$INPUT" | grep -q "rm -rf /"; then
  echo "BLOCKED: destructive command detected" >&2
  exit 2
fi
```

### Data Privacy

Everything you send (prompts, file contents, MCP results) goes to Anthropic's servers.

- Opt out of training data use: [claude.ai/settings/data-privacy-controls](https://claude.ai/settings/data-privacy-controls)
- Never paste production secrets, credentials, or PII into Claude sessions
- Use `.claude/settings.local.json` (gitignored) for sensitive local config

---

## 21. Community Tools

| Tool | Purpose | Install |
|------|---------|---------|
| **ccusage** | Cost tracking & daily reports | `bunx ccusage daily` |
| **RTK** | Token reduction (60–90% savings) | `brew install rtk-ai/tap/rtk` |
| **claude-code-viewer** | Browse session history in a UI | `npx @kimuson/claude-code-viewer` |
| **ccstatusline** | Enhanced terminal status line | Add to `settings.json` |
| **cc-sessions** | Search and resume sessions fast | [GitHub](https://github.com/FlorianBruniaux/cc-sessions) |
| **Entire CLI** | Checkpoints, approval gates, audit trails | [entire.io](https://entire.io) |

### Enhanced Status Line

Add to `~/.claude/settings.json`:

```json
{
  "statusLine": {
    "type": "command",
    "command": "npx -y ccstatusline@latest",
    "padding": 0
  }
}
```

### Fast Session Search (cc-sessions)

```bash
cs                    # List 10 most recent sessions
cs "authentication"   # Full-text search across sessions
```

---

## 22. The Golden Rules

1. **Always review diffs** before accepting changes
2. **Use `/compact`** before context hits 70% — don't wait until it's critical
3. **Be specific** in requests: WHAT, WHERE, HOW, VERIFY
4. **Start with Plan Mode** for complex, risky, or multi-file tasks
5. **Create `CLAUDE.md`** for every project you work in regularly
6. **Commit frequently** after each completed task (atomic commits)
7. **Know what's sent** — prompts, files, MCP results go to Anthropic ([opt-out](https://claude.ai/settings/data-privacy-controls))
8. **Use `claude -c`** as your default start command in active projects
9. **Switch models** at task boundaries, not mid-task
10. **Extract repeated patterns** into agents, skills, or commands

---

## 23. Common Issues Quick Fix

| Problem | Solution |
|---------|----------|
| "Command not found" | Check PATH; reinstall via `curl -fsSL https://claude.ai/install.sh \| sh` |
| Context too high (>70%) | Run `/compact` immediately |
| Slow or short responses | Run `/compact` or `/clear` |
| MCP server not working | `claude mcp list`, check config in `~/.claude.json` |
| Permission denied errors | Check `settings.local.json` permissions |
| Hook blocking unexpectedly | Check hook exit code logic; add debug logging |
| Lost session context | `claude -c` to resume, or check `~/.claude/tasks/` |
| Unexpected model behavior | Run `claude doctor` and update: `claude update` |

### Health Check (run these first)

```bash
# macOS / Linux
which claude && claude doctor && claude mcp list

# Windows PowerShell
where.exe claude; claude doctor; claude mcp list
```

---

## Resources

| Resource | Link |
|----------|------|
| Official Documentation | [docs.anthropic.com/claude-code](https://docs.anthropic.com/en/docs/claude-code) |
| Original Full Guide | [FlorianBruniaux/claude-code-ultimate-guide](https://github.com/FlorianBruniaux/claude-code-ultimate-guide) |
| Cheatsheet (1-page PDF) | [cheatsheet.md](https://github.com/FlorianBruniaux/claude-code-ultimate-guide/blob/main/guide/cheatsheet.md) |
| Community Tips | [Claudelog.com](https://claudelog.com/) |
| Claude Pricing | [claude.com/pricing](https://claude.com/pricing) |
| Privacy Controls | [claude.ai/settings/data-privacy-controls](https://claude.ai/settings/data-privacy-controls) |
| Official CHANGELOG | [anthropics/claude-code CHANGELOG](https://github.com/anthropics/claude-code/blob/main/CHANGELOG.md) |

---

*Guide compiled from [FlorianBruniaux/claude-code-ultimate-guide](https://github.com/FlorianBruniaux/claude-code-ultimate-guide) (v3.40.0 · May 2026). Original author: Florian BRUNIAUX. Licensed [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/).*
