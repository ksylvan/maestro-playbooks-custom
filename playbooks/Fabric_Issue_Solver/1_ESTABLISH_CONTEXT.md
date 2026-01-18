# Document 1: Establish Context

## Context

- **Playbook**: Fabric Issue Solver
- **Agent**: {{AGENT_NAME}}
- **Project**: {{AGENT_PATH}}
- **Loop**: {{LOOP_NUMBER}}
- **Date**: {{DATE}}
- **Working Folder**: {{AUTORUN_FOLDER}}

## Purpose

Establish the issue context and verify we're on a safe branch before proceeding.

## GitHub Issue

**GitHub Issue**: https://github.com/danielmiessler/fabric/issues/XXXX

> **NOTE**: Update the issue number above before running this playbook

## Additional Context

*(Add any background information, related discussions, or prior attempts here)*

- Related discussions:
- Slack/Discord notes:
- Previous attempts:
- Special considerations:

---

## Tasks

### Task 1: Check for Existing Context (Loop Awareness)

- [ ] **Check if context exists**: Look for `{{AUTORUN_FOLDER}}/ISSUE_CONTEXT.md`
  - If it exists AND has valid content, skip to Task 5 (already established)
  - If it doesn't exist or is empty, continue with Task 2

### Task 2: Branch Safety Check

**CRITICAL**: This playbook must NOT run on the `main` branch.

- [ ] **Verify current branch**:
  ```bash
  git branch --show-current
  ```

- [ ] **Check branch name**:
  - If on `main` or `master`: **STOP IMMEDIATELY**
    - Write error to `{{AUTORUN_FOLDER}}/BRANCH_ERROR.md`:
      ```markdown
      # Branch Safety Error

      **ERROR**: This playbook cannot run on the `main` branch.

      ## Required Action

      1. Create a feature branch:
         ```bash
         git checkout -b fix/issue-XXXX
         # or
         git checkout -b feature/describe-the-change
         ```

      2. Re-run this playbook

      ## Why?

      This playbook makes code changes. Running on `main` risks:
      - Accidental commits to protected branch
      - Merge conflicts
      - Breaking the main branch

      Always work on a feature/fix branch.
      ```
    - **HALT PLAYBOOK** - Do not proceed to other tasks

  - If on a feature/fix branch: Continue to Task 3

### Task 3: Fetch Issue Details

- [ ] **Get issue information**: Use GitHub CLI:
  ```bash
  gh issue view XXXX --repo danielmiessler/fabric
  ```

  Capture:
  - Issue title
  - Issue body/description
  - Labels
  - Assignees
  - Comments (if any)
  - Linked PRs (if any)

- [ ] **Check issue state**: Verify issue is open and not already resolved.

### Task 4: Gather Repository Context

- [ ] **Get current branch info**:
  ```bash
  git branch --show-current
  git log -1 --oneline
  ```

- [ ] **Check working directory status**:
  ```bash
  git status --short
  ```
  Note any uncommitted changes.

- [ ] **Identify base branch**: Usually `main` for Fabric.

### Task 5: Create Context Document

- [ ] **Write ISSUE_CONTEXT.md**: Create `{{AUTORUN_FOLDER}}/ISSUE_CONTEXT.md`:

```markdown
# Issue Context

## Issue Information
- **Number**: #XXXX
- **Title**: [Issue title]
- **URL**: https://github.com/danielmiessler/fabric/issues/XXXX
- **State**: [Open/Closed]
- **Labels**: [list of labels]
- **Created**: [date]
- **Author**: [username]

## Issue Description
[Full issue body/description]

## Issue Comments Summary
[Key points from comments, if any]

## Branch Information
- **Working Branch**: [current branch name]
- **Base Branch**: main
- **Latest Commit**: [commit hash and message]
- **Working Directory**: [clean/has changes]

## Additional Context
[Any background from the user input above]

## Related Issues/PRs
[List any linked issues or PRs]

## Initial Assessment
- **Issue Type**: [Bug fix / Feature / Enhancement / Documentation]
- **Estimated Complexity**: [Low / Medium / High]
- **Affected Areas**: [Initial guess at affected code areas]

## Context Established
- **Date**: {{DATE}}
- **Loop**: {{LOOP_NUMBER}}
```

### Task 6: Verify Context File

- [ ] **Confirm file created**: Verify `ISSUE_CONTEXT.md` exists and has content.

## Success Criteria

- Branch is NOT `main` (or halted with error)
- Issue details fetched from GitHub
- Repository context gathered
- `ISSUE_CONTEXT.md` created with all required sections

## Status

Mark complete when:
- Context document is created, OR
- Branch error document is created (halt condition)

---

**Next**: Document 2 will perform deep analysis of the issue.
