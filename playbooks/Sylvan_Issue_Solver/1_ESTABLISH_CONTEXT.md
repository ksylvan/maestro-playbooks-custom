# Document 1: Establish Context

## Context

- **Playbook**: Sylvan Issue Solver
- **Agent**: {{AGENT_NAME}}
- **Project**: {{AGENT_PATH}}
- **Loop**: {{LOOP_NUMBER}}
- **Date**: {{DATE}}
- **Working Folder**: {{AUTORUN_FOLDER}}

## Purpose

Establish the issue context and verify we're on a safe branch before proceeding.

## GitHub Issue

**GitHub Issue**: https://github.com/ksylvan/sylvan-website/issues/XXXX

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

**CRITICAL**: This playbook must NOT run on the `main` or `develop` branches.

- [ ] **Verify current branch**:
  ```bash
  git branch --show-current
  ```

- [ ] **Check branch name**:
  - If on `main`, `master`, or `develop`: **STOP IMMEDIATELY**
    - Write error to `{{AUTORUN_FOLDER}}/BRANCH_ERROR.md`:
      ```markdown
      # Branch Safety Error

      **ERROR**: This playbook cannot run on the `main` or `develop` branches.

      ## Required Action

      1. Create a feature branch:
         ```bash
         git checkout -b fix/issue-XXXX
         # or
         git checkout -b feature/describe-the-change
         ```

      2. Re-run this playbook

      ## Why?

      This playbook makes code changes. Running on `main` or `develop` risks:
      - Accidental commits to protected branches
      - Merge conflicts
      - Breaking the main or develop branch
      - Disrupting the deployment pipeline

      Always work on a feature/fix branch. PRs go to `develop` first.
      ```
    - **HALT PLAYBOOK** - Do not proceed to other tasks

  - If on a feature/fix branch: Continue to Task 3

### Task 3: Fetch Issue Details

- [ ] **Get issue information**: Use GitHub CLI:
  ```bash
  gh issue view XXXX --repo ksylvan/sylvan-website
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

- [ ] **Identify base branch**: `develop` for Sylvan (PRs go to develop first).

- [ ] **Verify Sylvan tech stack presence**:
  ```bash
  # Check for key Sylvan files
  ls package.json next.config.* tsconfig.json tailwind.config.* 2>/dev/null || echo "Not in Sylvan root"
  ```

### Task 5: Create Context Document

- [ ] **Write ISSUE_CONTEXT.md**: Create `{{AUTORUN_FOLDER}}/ISSUE_CONTEXT.md`:

```markdown
# Issue Context

## Issue Information
- **Number**: #XXXX
- **Title**: [Issue title]
- **URL**: https://github.com/ksylvan/sylvan-website/issues/XXXX
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
- **Base Branch**: develop
- **Latest Commit**: [commit hash and message]
- **Working Directory**: [clean/has changes]

## Sylvan Project Context

### Tech Stack
- **Framework**: Next.js 15+ (App Router)
- **UI Library**: React 19
- **Language**: TypeScript (strict mode)
- **Styling**: Tailwind CSS
- **Testing**: Vitest (unit), Playwright (E2E)
- **Deployment**: Vercel

### Key Directories
| Directory | Purpose |
|-----------|---------|
| `app/` | App Router pages and layouts |
| `components/` | React components |
| `lib/` | Utility functions and API clients |
| `styles/` | CSS/Tailwind styles |
| `public/` | Static assets |
| `__tests__/` | Unit test files |
| `e2e/` | Playwright E2E tests |

### Verification Commands
| Command | Purpose |
|---------|---------|
| `npm run dev` | Start development server |
| `npm run build` | Production build |
| `npm run type-check` | TypeScript type checking |
| `npm run test -- --run` | Run unit tests |
| `npm run test:e2e` | Run Playwright E2E tests |
| `npm run lint` | Run ESLint/Biome |
| `npm run deploy-info` | Check Vercel deployment status |

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

- Branch is NOT `main` or `develop` (or halted with error)
- Issue details fetched from GitHub
- Repository context gathered
- Sylvan-specific context documented
- `ISSUE_CONTEXT.md` created with all required sections

## Status

Mark complete when:
- Context document is created, OR
- Branch error document is created (halt condition)

---

**Next**: Document 2 will perform deep analysis of the issue.
