# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is a custom Maestro Playbooks repository containing reusable workflow configurations for the Maestro desktop application. Maestro orchestrates multiple AI coding agents (Claude Code, OpenAI Codex, OpenCode) and uses playbooks to batch-process markdown task documents.

## Playbook Architecture

Playbooks follow a **5-Document Chain Pattern**:

```
0_INITIALIZE.md  -> One-time setup (optional, runs once)
1_ANALYZE.md     -> Survey target, identify what to investigate
2_FIND_*.md      -> Find specific issues/gaps/entities
3_EVALUATE.md    -> Rate candidates by priority criteria
4_IMPLEMENT.md   -> Execute ONE item per loop iteration
5_PROGRESS.md    -> Loop gate: resets 1-4 if work remains, exits if done
```

**Loop Control**: Only document 5 should have `resetOnCompletion: true` in manifest.json.

## Document Requirements

Every playbook document must include a context block:

```markdown
# Document Title

## Context
- **Playbook:** Playbook Name
- **Agent:** {{AGENT_NAME}}
- **Project:** {{AGENT_PATH}}
- **Loop:** {{LOOP_NUMBER}}
- **Date:** {{DATE}}
- **Working Folder:** {{AUTORUN_FOLDER}}
```

Use checkbox format (`- [ ]`) for tasks that Auto Run will process.

## Template Variables

Key variables substituted at runtime:
- `{{AGENT_NAME}}`, `{{AGENT_PATH}}` - Agent identifier and project path
- `{{LOOP_NUMBER}}` - Current iteration count (starts at 1)
- `{{AUTORUN_FOLDER}}` - Documents directory path
- `{{GIT_BRANCH}}` - Current git branch
- `{{DATE}}`, `{{TIME}}`, `{{DATETIME}}` - Timestamps

## Folder Structure

```
playbooks/
└── playbook-name.md    # Standalone task documents
    # OR for full playbooks:
    └── PlaybookName/
        ├── README.md           # Required: Description for exchange
        ├── 1_ANALYZE.md
        ├── 2_FIND_*.md
        ├── 3_EVALUATE.md
        ├── 4_IMPLEMENT.md
        ├── 5_PROGRESS.md
        ├── Agent-Prompt.md     # Optional: Custom AI prompt
        └── assets/             # Optional: Config files, scripts
```

## Linting

Markdown linting is configured via `.markdownlint.json`. Disabled rules include line length (MD013), heading spacing (MD022/MD031/MD032), duplicate headings (MD024), fenced code language (MD040), and first-line heading (MD041).
