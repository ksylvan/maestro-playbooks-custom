# The Complete Maestro & Playbooks Guide

This document is your comprehensive reference for becoming an expert at Maestro and Maestro Playbooks. Read this thoroughly before working on any Maestro-related tasks.

---

## Table of Contents

1. [What is Maestro?](#what-is-maestro)
2. [Core Concepts](#core-concepts)
3. [Auto Run & Playbooks Deep Dive](#auto-run--playbooks-deep-dive)
4. [Playbook Architecture](#playbook-architecture)
5. [Template Variables Reference](#template-variables-reference)
6. [Creating Playbooks](#creating-playbooks)
7. [Keyboard Shortcuts](#keyboard-shortcuts)
8. [Slash Commands](#slash-commands)
9. [Git Worktrees](#git-worktrees)
10. [Group Chat](#group-chat)
11. [CLI Reference](#cli-reference)
12. [Context Management](#context-management)
13. [Remote Access](#remote-access)
14. [Best Practices](#best-practices)
15. [Example Playbooks in This Repository](#example-playbooks-in-this-repository)

---

## What is Maestro?

**Maestro** is a cross-platform desktop application for orchestrating multiple AI coding agents. Its core philosophy: *"Maestro hones fractured attention into focused intent."*

### Key Capabilities

- **Multi-Agent Management**: Run unlimited agents in parallel (Claude Code, OpenAI Codex, OpenCode)
- **Auto Run**: Batch-process markdown task documents through AI agents
- **Playbooks**: Reusable, shareable workflow configurations
- **Git Worktrees**: Parallel development on isolated branches
- **Group Chat**: Coordinate multiple agents in a single conversation
- **Keyboard-First Design**: Optimized for power users who live on the keyboard

### Supported AI Agents

| Agent | Description |
|-------|-------------|
| Claude Code | Anthropic's AI coding assistant |
| OpenAI Codex | OpenAI's coding agent |
| OpenCode | Open-source AI coding assistant |

Future support planned for Aider, Gemini CLI, and Qwen3 Coder based on demand.

---

## Core Concepts

### Dual-Mode Sessions

Every agent has two terminals:
- **AI Terminal**: Conversation with the AI agent
- **Command Terminal**: Standard shell for running commands

Switch between them with `Cmd+J` (Mac) / `Ctrl+J` (Windows/Linux).

### Session Isolation

Each Auto Run task executes in a **completely fresh AI session** with:
- Clean context (no conversation history bleed)
- Unique session ID
- Predictable, reproducible behavior

This is critical for looping playbooks where each iteration must be independent.

### Message Queueing

You can queue messages while the AI is busy. They're sent automatically when the agent becomes ready.

---

## Auto Run & Playbooks Deep Dive

### What is Auto Run?

Auto Run is a **file-system-based document runner** that batch-processes markdown tasks using AI agents. Each document with checkboxes spawns a fresh AI session.

### How It Works

1. Navigate to Auto Run tab (`Cmd+Shift+1`)
2. Select folder containing markdown task documents
3. Configure documents, order, and execution options
4. Click Run to start batch processing

### Document Structure

Documents are markdown files with checkbox tasks:

```markdown
# Task Document Title

## Context
- **Agent:** {{AGENT_NAME}}
- **Project:** {{AGENT_PATH}}
- **Loop:** {{LOOP_NUMBER}}

## Tasks

- [ ] First task to complete
- [ ] Second task to complete
- [ ] Third task to complete
```

Use `Cmd+L` / `Ctrl+L` to quickly insert checkboxes.

### Key Configuration Options

| Option | Description |
|--------|-------------|
| **Reset on Completion** | Creates working copies in `Runs/` subfolder instead of modifying originals. Generates timestamped logs. |
| **Loop Mode** | Cycles back to first document after completing the last |
| **Max Loops** | Prevents runaway iterations |
| **Duplicate** | Add same document multiple times to the queue |

### What Are Playbooks?

Playbooks are **saved Auto Run configurations** that automate multi-step workflows. Each playbook defines:
- A sequence of markdown documents
- Execution settings (loop mode, max loops)
- Reset behavior per document
- Optional custom agent prompts

### Playbook Exchange

Community repository of pre-built playbooks accessible via:
- Quick Actions (`Cmd+K`) > "Playbook Exchange"
- Exchange button in Auto Run panel

---

## Playbook Architecture

### The 5-Document Chain Pattern

Every playbook follows this structure:

```
0_INITIALIZE.md  -> (Optional) One-time setup, runs once
1_ANALYZE.md     -> Survey target, identify what to investigate
2_FIND_*.md      -> Find specific issues/gaps/entities
3_EVALUATE.md    -> Rate candidates by priority criteria
4_IMPLEMENT.md   -> Execute ONE item per loop iteration
5_PROGRESS.md    -> Loop gate: resets 1-4 if work remains, exits if done
```

### Loop Control Mechanism

| Document | Reset Setting | Behavior |
|----------|--------------|----------|
| 0_INITIALIZE.md | `Reset: OFF` | Runs once at start |
| 1-4 Documents | `Reset: OFF` | Don't auto-reset when completed |
| 5_PROGRESS.md | `Reset: ON` | Controls the loop by conditionally resetting 1-4 |

**The key insight**: Document 5 is the **progress gate**. It checks if work remains and resets documents 1-4 to continue, or leaves them alone to exit.

### Exit Conditions

Exit when ALL of these are true:
1. No `PENDING` items remain in the plan
2. All tactics/discovery methods are exhausted
3. Or: max loops reached

### Design Philosophy: Context Engineering

**Playbook design IS context engineering.** The core principle is **progressive disclosure**:

> Reveal information incrementally, not all at once.

AI agents perform best with focused, relevant context. Playbooks achieve this through:

1. **Staged discovery**: Each document produces artifacts for the next stage
2. **Front-loaded work**: Token-heavy discovery happens upfront in documents 1-3
3. **Cheap execution**: Document 4 reads pre-computed context, executes one item
4. **Self-contained iterations**: Each loop is discover → plan → execute → check

### Working Files

Each loop creates numbered working files:

| File | Purpose |
|------|---------|
| `LOOP_N_GAME_PLAN.md` | Investigation tactics and strategy |
| `LOOP_N_CANDIDATES.md` | Raw findings from discovery |
| `LOOP_N_PLAN.md` | Evaluated items with priority ratings |
| `*_LOG.md` | Cumulative log across all loops |

### Status Values

Items in plan files use these statuses:

| Status | Meaning |
|--------|---------|
| `PENDING` | Ready for automated processing |
| `IMPLEMENTED` / `RESEARCHED` | Completed in current loop |
| `WON'T DO` / `SKIP` | Intentionally skipped |
| `PENDING - MANUAL REVIEW` | Requires human decision |

---

## Template Variables Reference

Maestro substitutes these variables at runtime:

### Agent Variables

| Variable | Description |
|----------|-------------|
| `{{AGENT_NAME}}` | Agent identifier |
| `{{AGENT_PATH}}` | Full project directory path |
| `{{AGENT_GROUP}}` | Group assignment (if applicable) |
| `{{AGENT_SESSION_ID}}` | Conversation continuity identifier |
| `{{TAB_NAME}}` | Custom tab label (alias: `{{SESSION_NAME}}`) |
| `{{TOOL_TYPE}}` | Agent type: `claude-code`, `codex`, `opencode` |

### Path Variables

| Variable | Description |
|----------|-------------|
| `{{CWD}}` | Present working directory |
| `{{AUTORUN_FOLDER}}` | Auto Run documents directory |

### Auto Run Variables

| Variable | Description |
|----------|-------------|
| `{{DOCUMENT_NAME}}` | Current document title (no extension) |
| `{{DOCUMENT_PATH}}` | Complete document location |
| `{{LOOP_NUMBER}}` | Iteration count (starts at 1) |

### Date/Time Variables

| Variable | Description |
|----------|-------------|
| `{{DATE}}` | Full date |
| `{{TIME}}` | Full time |
| `{{DATETIME}}` | Combined date and time |
| `{{TIMESTAMP}}` | Unix milliseconds |
| `{{DATE_SHORT}}` | Abbreviated date |
| `{{TIME_SHORT}}` | Abbreviated time |
| `{{YEAR}}`, `{{MONTH}}`, `{{DAY}}` | Individual components |
| `{{WEEKDAY}}` | Day of week |

### Git Variables

| Variable | Description |
|----------|-------------|
| `{{GIT_BRANCH}}` | Current branch name |
| `{{IS_GIT_REPO}}` | Boolean status |

### Context Variables

| Variable | Description |
|----------|-------------|
| `{{CONTEXT_USAGE}}` | Token window percentage |

---

## Creating Playbooks

### Folder Structure

```
Category/
└── YourPlaybook/
    ├── README.md           # Required: Description shown in exchange
    ├── 0_INITIALIZE.md     # Optional: One-time setup
    ├── 1_ANALYZE.md        # Survey and identify targets
    ├── 2_FIND_*.md         # Find specific issues/gaps
    ├── 3_EVALUATE.md       # Rate candidates by priority
    ├── 4_IMPLEMENT.md      # Execute one item per loop
    ├── 5_PROGRESS.md       # Loop gate: continue or exit
    ├── Agent-Prompt.md     # Optional: Custom prompt
    └── assets/             # Optional: Config files, scripts, etc.
```

### Required Context Block

Every document must include:

```markdown
# Document Title

## Context
- **Playbook:** Your Playbook Name
- **Agent:** {{AGENT_NAME}}
- **Project:** {{AGENT_PATH}}
- **Loop:** {{LOOP_NUMBER}}
- **Date:** {{DATE}}
- **Working Folder:** {{AUTORUN_FOLDER}}
```

### Document Guidelines

1. **Use checkbox format**: `- [ ]` for Auto Run to process
2. **Be specific**: Clear, actionable instructions
3. **One item per loop**: Document 4 should implement ONE item, not batch
4. **Clear exit conditions**: Document 5 must have unambiguous logic

### Assets Folder

Bundle non-markdown files with your playbook:

```markdown
- [ ] Read the config from `{{AUTORUN_FOLDER}}/assets/config.yaml`
```

Use cases:
- Configuration templates (YAML, JSON, TOML)
- Dockerfiles
- Helper scripts
- Schema definitions

### manifest.json Entry

```json
{
  "id": "category-subcategory",
  "title": "Your Playbook Title",
  "description": "One or two sentences describing the playbook.",
  "category": "Category",
  "subcategory": "Subcategory",
  "author": "Your Name",
  "authorLink": "https://your-website.com",
  "tags": ["relevant", "keywords"],
  "lastUpdated": "YYYY-MM-DD",
  "path": "Category/Subcategory",
  "documents": [
    { "filename": "1_ANALYZE", "resetOnCompletion": false },
    { "filename": "2_FIND_ISSUES", "resetOnCompletion": false },
    { "filename": "3_EVALUATE", "resetOnCompletion": false },
    { "filename": "4_IMPLEMENT", "resetOnCompletion": false },
    { "filename": "5_PROGRESS", "resetOnCompletion": true }
  ],
  "loopEnabled": true,
  "maxLoops": null,
  "prompt": null
}
```

**Important**: Only document 5 should have `resetOnCompletion: true`.

---

## Keyboard Shortcuts

### Essential Shortcuts

| Action | macOS | Windows/Linux |
|--------|-------|---------------|
| Quick Actions | `Cmd+K` | `Ctrl+K` |
| New Agent | `Cmd+N` | `Ctrl+N` |
| Switch AI/Terminal | `Cmd+J` | `Ctrl+J` |
| Previous Agent | `Cmd+[` | `Ctrl+[` |
| Next Agent | `Cmd+]` | `Ctrl+]` |
| Toggle Sidebar | `Cmd+B` | `Ctrl+B` |
| All Shortcuts | `Cmd+/` | `Ctrl+/` |

### Navigation

| Action | macOS | Windows/Linux |
|--------|-------|---------------|
| Toggle Right Panel | `Cmd+\` | `Ctrl+\` |
| Jump to Agent 1-9 | `Opt+Cmd+NUMBER` | `Alt+Ctrl+NUMBER` |
| Open Settings | `Cmd+,` | `Ctrl+,` |

### Agent Management

| Action | macOS | Windows/Linux |
|--------|-------|---------------|
| Kill Agent | `Cmd+Shift+Backspace` | `Ctrl+Shift+Backspace` |
| Move Agent to Group | `Cmd+Shift+M` | `Ctrl+Shift+M` |

### Tab Management

| Action | macOS | Windows/Linux |
|--------|-------|---------------|
| New Tab | `Cmd+T` | `Ctrl+T` |
| Close Tab | `Cmd+W` | `Ctrl+W` |
| Reopen Tab | `Cmd+Shift+T` | `Ctrl+Shift+T` |
| Next/Previous Tab | `Cmd+Shift+]` / `[` | `Ctrl+Shift+]` / `[` |

### File Operations

| Action | macOS | Windows/Linux |
|--------|-------|---------------|
| Go to File | `Cmd+G` | `Ctrl+G` |
| Files Tab | `Cmd+Shift+F` | `Ctrl+Shift+F` |
| History Tab | `Cmd+Shift+H` | `Ctrl+Shift+H` |
| Document Graph | `Cmd+Shift+G` | `Ctrl+Shift+G` |
| Usage Dashboard | `Opt+Cmd+U` | `Alt+Ctrl+U` |

### AI Terminal

| Action | macOS | Windows/Linux |
|--------|-------|---------------|
| Toggle History | `Cmd+S` | `Ctrl+S` |
| Toggle Read-Only | `Cmd+R` | `Ctrl+R` |
| Toggle Thinking | `Cmd+Shift+K` | `Ctrl+Shift+K` |
| Insert Checkbox | `Cmd+L` | `Ctrl+L` |

---

## Slash Commands

### Using Slash Commands

Type `/` in the input area, then navigate with arrow keys and select with `Tab` or `Enter`.

### Creating Custom Commands

Settings > Custom AI Commands. Each command has:
- **Trigger**: e.g., `/deploy`
- **Prompt**: AI-directed prompt with template variables

Example:
```
/standup → "It's {{WEEKDAY}}, {{DATE}}. I'm on branch {{GIT_BRANCH}} at {{AGENT_PATH}}."
```

### Built-In Command Suites

**Spec-Kit Commands** (GitHub's structured methodology):
- `/speckit.constitution` - Project constitution
- `/speckit.specify` - Specification creation
- `/speckit.clarify` - Requirement clarification
- `/speckit.plan` - Implementation planning
- `/speckit.tasks` - Task breakdown
- `/speckit.implement` - Implementation

**OpenSpec Commands** (spec-driven changes):
- `/openspec.proposal` - Change proposal with spec deltas
- `/openspec.apply` - Apply approved proposals
- `/openspec.archive` - Post-deployment archival
- `/openspec.implement` - Auto Run document generation

### Claude Code Commands in Auto Run

**Supported in Batch Mode**:
`/compact`, `/context`, `/cost`, `/init`, `/pr-comments`, `/release-notes`, `/review`, `/security-review`, plus custom plugin commands

**Interactive-Only (Not Supported)**:
`/mcp`, `/help`, `/clear`, `/config`, `/model`, `/permissions`, `/memory`, `/rewind`, `/vim`, `/doctor`, `/login`, `/logout`, `/bug`

---

## Git Worktrees

### What Are Git Worktrees?

Git worktrees enable parallel development by running AI agents on separate branches in isolated directories. No conflicts between parallel work streams.

### Creating a Worktree Sub-Agent

1. Hover over an agent in a git repository
2. Click the git branch indicator
3. Select "Create Worktree Sub-Agent"
4. Configure:
   - **Worktree Directory**: Base folder (outside main repo recommended)
   - **Branch Name**: Becomes the subdirectory name

### Best Practice

> Configure the worktree directory outside your main repository (e.g., `~/Projects/Maestro-WorkTrees/`). This keeps worktrees organized and prevents them from appearing in your main repo's file tree.

### Use Cases

- **Background Auto Run**: Execute automated tasks while working interactively
- **Feature Branches**: Dedicated agents for each feature
- **Code Review**: Review PRs without branch switching
- **Parallel Experiments**: Test multiple approaches simultaneously

### Pull Request Workflow

1. Complete work in worktree
2. Right-click agent or use `Cmd+K`
3. Create PR (requires `gh` CLI installed and authenticated)

---

## Group Chat

### What is Group Chat?

Coordinate multiple AI agents in a single conversation. A **moderator AI** orchestrates discussions, routing questions to appropriate agents and synthesizing responses.

### How It Works

1. Create Group Chat from sidebar
2. Add participants with @mentions: `@Frontend`, `@Backend`
3. Send your question
4. Moderator routes to relevant agents
5. Each agent responds in their project context
6. Moderator synthesizes into coherent answer

### Moderator Responsibilities

- Direct answers for simple questions
- Delegation to appropriate agents
- Follow-up until complete
- Synthesis of multiple perspectives

### Use Cases

- Cross-project architecture discussions
- Comparative analysis across repositories
- Knowledge synthesis from specialized agents
- Cross-machine collaboration via SSH

---

## CLI Reference

### Installation

**macOS:**
```bash
printf '#!/bin/bash\nnode "/Applications/Maestro.app/Contents/Resources/maestro-cli.js" "$@"\n' | sudo tee /usr/local/bin/maestro-cli && sudo chmod +x /usr/local/bin/maestro-cli
```

**Linux:**
```bash
printf '#!/bin/bash\nnode "/opt/Maestro/resources/maestro-cli.js" "$@"\n' | sudo tee /usr/local/bin/maestro-cli && sudo chmod +x /usr/local/bin/maestro-cli
```

### Core Commands

| Command | Purpose |
|---------|---------|
| `maestro-cli list groups` | Display all groups |
| `maestro-cli list agents` | Show all agents |
| `maestro-cli list agents --group <id>` | Filter agents by group |
| `maestro-cli show agent <id>` | Display agent details |
| `maestro-cli list playbooks` | List all playbooks |
| `maestro-cli show playbook <id>` | Display playbook details |
| `maestro-cli playbook <id>` | Execute a playbook |

### Playbook Execution Options

| Flag | Description |
|------|-------------|
| `--dry-run` | Preview without executing |
| `--no-history` | Execute without recording |
| `--wait` | Wait for agent if busy |
| `--verbose` | Detailed output |
| `--debug` | Debug mode |
| `--json` | JSONL output for scripting |

### Scheduling with Cron

```bash
0 * * * * /usr/local/bin/maestro-cli playbook <id> --json >> /var/log/maestro.jsonl 2>&1
```

---

## Context Management

### Context Thresholds

| Usage | Indicator |
|-------|-----------|
| <60% | Normal |
| 60% | Yellow warning |
| 80% | Red warning |

### Context Compaction

When approaching limits, compact context to preserve:
- Key decisions
- Code modifications
- Technical constraints
- Task state

**Eligibility**: ≥25% context usage or ≥10,000 tokens

**Process**:
- Small conversations (<50k tokens): Single-pass summarization
- Large conversations (≥50k tokens): Chunked processing with consolidation

### Session Merging

Combine context from multiple tabs/sessions:
- Right-click menu > Merge sessions
- Modal shows token estimates
- Optional "clean context" to remove duplicates

### Sending to Another Agent

Transfer context to different AI agents:
- Useful for perspective shifts
- Accessing different tool sets
- Working on different project directories

---

## Remote Access

### Built-in Web Server

Runs on random port with auto-generated security token. Access via QR code from mobile devices.

### Local Network Access

Click "OFFLINE" button in header to:
- Show QR code
- Copy secure URL
- Connect from any device on same network

### Cloudflare Tunnels

For remote access outside local network:
1. Install `cloudflared` CLI
2. Toggle to remote mode in QR overlay
3. No time limits, no account required

### Mobile Interface Features

- Real-time session monitoring
- Light/dark mode
- Automatic reconnection
- Offline command queuing
- Swipe gestures
- Quick action menus

---

## Best Practices

### Running Playbooks

1. **Start without loop mode**: Review what it finds before automation
2. **Set max loops**: Prevent runaway iterations
3. **Review-first approach**:
   - Run once with Loop Mode OFF
   - Review generated plan files
   - Adjust statuses if needed
   - Run again with Loop Mode ON

### Designing Playbooks

1. **Context is king**: Each document should answer "What is the minimum context needed?"
2. **Front-load discovery**: Token-heavy work in documents 1-3
3. **One item per loop**: Document 4 implements ONE item for clean git commits
4. **Clear exit conditions**: Document 5 must have unambiguous logic
5. **Use template variables**: Make playbooks portable across projects

### General Workflow

1. **Commit frequently**: Each loop iteration is a good commit point
2. **Run tests after changes**: Verify nothing broke
3. **Compact context proactively**: Don't wait for warnings
4. **Use worktrees for parallel work**: Avoid conflicts
5. **Descriptive agent names**: Makes Group Chat routing clearer

### Session Hook Tip

Use `MAESTRO_SESSION_RESUMED` to skip agent hooks on resumed sessions:

```bash
[ "$MAESTRO_SESSION_RESUMED" = "1" ] && exit 0
# ... hook logic for new sessions only
```

---

## Available Development Playbooks

| Playbook | Purpose | Exit Condition |
|----------|---------|----------------|
| `Development/Performance/` | Find and fix performance issues | No PENDING items remain |
| `Development/Security/` | Audit and remediate vulnerabilities | No CRITICAL/HIGH severity issues |
| `Development/Refactor/` | Simplify code, eliminate duplication | No LOW risk + HIGH benefit items |
| `Development/Documentation/` | Achieve 90% doc coverage | Coverage ≥90% |
| `Development/Testing/` | Achieve 80% test coverage | Coverage ≥80% |
| `Development/Usage/` | Update README to match features | No CRITICAL/HIGH gaps |
| `Development/Mobile-Polish/` | Transform desktop sites for mobile | All mobile issues resolved |

## Research Playbooks

| Playbook | Purpose | Notes |
|----------|---------|-------|
| `Research/Market/` | Build Obsidian-style knowledge vault | Requires custom Agent-Prompt.md configuration |

---

## Example Playbooks in This Repository

This repository includes three example playbooks demonstrating different patterns:

### Linear Feature Implementation: AFG Activity Tracking

**Path**: `playbooks/AFG_Website_Tracking_Feature/`

A 6-document sequential playbook that implements a complete feature from start to finish. Demonstrates the **linear pattern** (no looping) for feature implementation.

| Document | Purpose |
|----------|---------|
| `1_MIGRATION_INFRASTRUCTURE` | Create Django/Rails-style migration runner |
| `2_UPDATE_DATABASE` | Schema, migration file, TypeScript types |
| `3_ADD_QUERIES` | Database query functions |
| `4_TRACK_ACTIVITY` | Auth event handler and activity tracking |
| `5_ADMIN_UI` | Admin activity page and dashboard |
| `6_USER_UI` | User-facing profile and home page updates |

**Key characteristics:**
- `loopEnabled: false` — runs once, sequentially
- All documents have `resetOnCompletion: false`
- Each document builds on the previous one
- Includes migration infrastructure for zero-downtime deployments

### Linear Code Review: Code Review

**Path**: `playbooks/Code_Review/`

A 5-document sequential playbook for comprehensive pull request reviews. Demonstrates a **multi-pass review pattern** covering different aspects of code quality.

| Document | Purpose |
|----------|---------|
| `1_ANALYZE_CHANGES` | Understand PR scope, categorize files |
| `2_REVIEW_CODE` | Correctness, best practices, error handling |
| `3_CHECK_SECURITY` | OWASP Top 10, secrets, auth/authz |
| `4_VERIFY_TESTS` | Test coverage gaps, test quality |
| `5_SUMMARIZE` | Compile findings, generate verdict |

**Key characteristics:**
- `loopEnabled: false` — single-pass review
- Generates working files: `REVIEW_SCOPE.md`, `CODE_ISSUES.md`, `SECURITY_ISSUES.md`, `TEST_GAPS.md`, `REVIEW_SUMMARY.md`
- Outputs ready-to-post PR comment
- Update PR URL in Document 1 before running

### Comparative Analysis: Best PR Comparison

> Note: This playbook is now in [Maestro](https://github.com/pedramamini/Maestro-Playbooks) as of 1/10/2026; See <https://github.com/pedramamini/Maestro-Playbooks/pull/9>.

**Path**: `playbooks/Best-PR/`

A 5-document playbook that objectively compares two competing PRs implementing the same feature. Uses **weighted scoring** to determine a winner and extracts valuable insights from the runner-up.

| Document | Purpose |
|----------|---------|
| `1_ESTABLISH_CONTEXT` | Fetch both PRs, understand feature scope |
| `2_EVALUATE_PR_A` | Score PR-A against weighted criteria |
| `3_EVALUATE_PR_B` | Score PR-B against same criteria |
| `4_COMPARE` | Side-by-side matrix, determine winner |
| `5_SYNTHESIZE` | Final recommendation + cross-pollination |

**Key characteristics:**
- `loopEnabled: false` — single comparison pass
- Weighted scoring: Security/Correctness (3x), Performance/Tests (2x), Quality/Docs/Size (1x)
- Generates: `EVALUATION_CRITERIA.md`, `PR_A_EVALUATION.md`, `PR_B_EVALUATION.md`, `COMPARISON_MATRIX.md`, `BEST_PR_SUMMARY.md`
- Supports "Too Close to Call" outcome when scores are within 5 points
- Extracts "gems" from runner-up for cross-pollination

### When to Use Each Pattern

| Pattern | Use When | Example |
|---------|----------|---------|
| **Linear (no loop)** | Sequential steps that build on each other | Feature implementation, migrations |
| **Comparative** | Evaluating multiple options against criteria | PR comparison, tech stack evaluation |
| **Looping (5-doc chain)** | Iterative discovery and implementation | Bug fixing, refactoring, documentation gaps |

---

## Quick Reference Links

- **Maestro Docs**: <https://docs.runmaestro.ai/>
- **Maestro GitHub**: <https://github.com/pedramamini/Maestro>
- **Playbooks GitHub**: <https://github.com/pedramamini/Maestro-Playbooks>
- **Discord**: <https://runmaestro.ai/discord>

---

## Example: Document 5 (Progress Gate) Logic

This is the critical loop control document. Here's the decision logic:

```
IF plan file doesn't exist:
    → Do NOT reset (pipeline just started)

ELSE IF items with status "PENDING" exist:
    → Reset documents 1-4 (continue to implement)

ELSE IF candidates file does NOT contain "ALL_TACTICS_EXHAUSTED":
    → Reset documents 1-4 (continue to discover)

ELSE:
    → Do NOT reset (exit - all done)
```

**Key insight**: The loop continues if EITHER:
1. There are PENDING items to implement, OR
2. There are still tactics to execute

---

*This guide was compiled from the official Maestro documentation, GitHub repositories, and CONTRIBUTING.md. Keep it updated as Maestro evolves.*
