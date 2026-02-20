# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is a custom Maestro Playbooks repository containing reusable workflow configurations for the Maestro desktop application. Maestro orchestrates multiple AI coding agents (Claude Code, OpenAI Codex, OpenCode) and uses playbooks to batch-process markdown task documents.

## Playbook Patterns

### Linear Pattern (non-looping)
Sequential documents that build on each other. Each runs once. Used for feature implementation, code review, and comparative analysis.

```
1_FIRST_STEP.md  -> 2_SECOND_STEP.md -> ... -> N_FINAL_STEP.md
```

All documents have `loopEnabled: false` and `resetOnCompletion: false`.

### Iterative Pattern (looping)
Discovery and implementation cycles that repeat until complete:

```
1_ANALYZE.md     -> Survey target, identify what to investigate
2_FIND_*.md      -> Find specific issues/gaps/entities
3_EVALUATE.md    -> Rate candidates by priority criteria
4_IMPLEMENT.md   -> Execute ONE item per loop iteration (resetOnCompletion: true)
5_PROGRESS.md    -> Verify and test (resetOnCompletion: true)
6_GATE.md        -> Loop gate + finalize when done (resetOnCompletion: false)
```

**Loop Control**: Documents 4-5 have `resetOnCompletion: true` (Maestro auto-resets them each iteration). Document 6 is the gate — it has `resetOnCompletion: false` and is the only non-reset document in the loop. Maestro continues looping as long as any non-reset document has unchecked tasks.

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

## Working Files

Documents generate output files in `{{AUTORUN_FOLDER}}`:
- Use descriptive names: `REVIEW_SCOPE.md`, `CODE_ISSUES.md`, `COMPARISON_MATRIX.md`
- For looping playbooks: `LOOP_N_PLAN.md`, `LOOP_N_CANDIDATES.md`

### Status Values (for iterative playbooks)
- `PENDING` - Ready for automated processing
- `IMPLEMENTED` / `RESEARCHED` - Completed
- `WON'T DO` / `SKIP` - Intentionally skipped
- `PENDING - MANUAL REVIEW` - Requires human decision

## Folder Structure

```
playbooks/
├── manifest.json              # REQUIRED: All playbook definitions
└── PlaybookName/
    ├── README.md              # Required: Description for exchange
    ├── 1_FIRST_STEP.md
    ├── 2_SECOND_STEP.md
    ├── ...
    ├── Agent-Prompt.md        # Optional: Custom AI prompt
    └── assets/                # Optional: Config files, scripts
```

## Manifest Configuration

All playbooks are defined in `playbooks/manifest.json`. Each entry:

```json
{
  "id": "playbook-id",
  "title": "Playbook Title",
  "path": "PlaybookFolderName",
  "documents": [
    { "filename": "1_FIRST_STEP", "resetOnCompletion": false },
    { "filename": "2_SECOND_STEP", "resetOnCompletion": false }
  ],
  "loopEnabled": false,
  "maxLoops": null
}
```

## Linting

Markdown linting configured via `.markdownlint.json`. Disabled: line length (MD013), heading spacing (MD022/MD031/MD032), duplicate headings (MD024), fenced code language (MD040), first-line heading (MD041), code block indentation (MD060).
