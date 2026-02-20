# Fabric Issue Solver Loop Gate

## Context

- **Playbook:** Fabric Issue Solver
- **Agent:** {{AGENT_NAME}}
- **Project:** {{AGENT_PATH}}
- **Loop:** {{LOOP_NUMBER}}
- **Date:** {{DATE}}
- **Working Folder:** {{AUTORUN_FOLDER}}

## Purpose

This is the **loop gate** and **finalization** document. It has `resetOnCompletion: false` and is the only non-reset document in the implementation loop. Maestro continues looping as long as this document has unchecked tasks. When all tasks are checked off, the loop exits.

## Gate Decision

Read `{{AUTORUN_FOLDER}}/IMPLEMENTATION_PLAN.md` and check phase statuses:

- If any phases have status `PENDING` → **leave the task below unchecked** (loop continues)
- If no phases have status `PENDING` (all are COMPLETE, FAILED, or BLOCKED) → **check the task below** and proceed to finalization

- [ ] **All implementation phases resolved** — No PENDING phases remain in `IMPLEMENTATION_PLAN.md`. All phases have been completed, failed, or blocked. Display a summary: (1) COMPLETE count, (2) PENDING count, (3) FAILED/BLOCKED count, (4) decision.

## Finalization

The following tasks finalize the implementation and prepare the PR.

- [ ] **Update progress tracking table** in `IMPLEMENTATION_PLAN.md`:

```markdown
## Progress Tracking

| Phase | Title | Status | Started | Completed |
|-------|-------|--------|---------|-----------|
| 1 | [Title] | COMPLETE | [date] | [date] |
| 2 | [Title] | PENDING | - | - |
| ... | ... | ... | ... | ... |

## Loop Summary
- **Current Loop**: {{LOOP_NUMBER}}
- **Phases Completed**: X of Y
- **Next Action**: Finalization
```

- [ ] **Final test run**: Confirm all tests still pass:
  ```bash
  go test ./...
  ```

- [ ] **Review commit history**:
  ```bash
  git log --oneline origin/main..HEAD
  ```

- [ ] **Create final commit** (if changes aren't committed):
  ```bash
  git add -A
  git commit -m "Fix #XXXX: [brief description]

  [More detailed description of the fix]

  - [Change 1]
  - [Change 2]
  - [Change 3]

  Closes #XXXX"
  ```

- [ ] **Collect all changes**: Review all `PHASE_N_IMPLEMENTATION.md` files. List modified files:
  ```bash
  git diff --stat origin/main..HEAD
  ```

- [ ] **Write PR_DESCRIPTION.md**: Create `{{AUTORUN_FOLDER}}/PR_DESCRIPTION.md` with:
  - Summary (2-3 sentences)
  - Related issue reference (Closes #XXXX)
  - Changes made (categorized)
  - Implementation approach
  - Files changed table
  - Testing section (automated + manual steps)
  - Checklist (coding conventions, tests, no secrets, focused PR)

  Keep it concise. Base all content on actual implementation files.

- [ ] **Create the pull request** using content from `PR_DESCRIPTION.md`. Save the PR number for changelog generation.

- [ ] **Generate changelog** using the generate_changelog tool:
  ```bash
  go run ./cmd/generate_changelog --ai-summarize --incoming-pr <PR_NUMBER>
  ```

- [ ] **Push the newly generated changelog snippet**:
  ```bash
  git push
  ```

- [ ] **Write COMPLETION_SUMMARY.md**: Create `{{AUTORUN_FOLDER}}/COMPLETION_SUMMARY.md` with:
  - Issue details (number, title, URL)
  - Resolution status, branch, date, loop count
  - Implementation statistics (phases, files, lines changed)
  - Files changed list
  - Key changes summary
  - Testing results
  - List of generated artifacts

- [ ] **Confirm all artifacts exist**:
  - `ISSUE_CONTEXT.md`
  - `ISSUE_ANALYSIS.md`
  - `IMPLEMENTATION_PLAN.md`
  - `PHASE_*_IMPLEMENTATION.md` (one per phase)
  - `TEST_RESULTS.md`
  - `PR_DESCRIPTION.md`
  - `COMPLETION_SUMMARY.md`

  Report any missing files.

- [ ] **Verify branch is ready**:
  ```bash
  git status
  git log --oneline origin/main..HEAD
  ```

## Notes

- The changelog is generated using the project's `generate_changelog` tool after PR creation.
