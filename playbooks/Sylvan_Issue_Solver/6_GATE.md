# Sylvan Issue Solver Loop Gate

## Context

- **Playbook:** Sylvan Issue Solver
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

The following tasks run E2E tests, deploy a preview, and create the PR.

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

- [ ] **Final unit test run**: Confirm all tests still pass:
  ```bash
  npm run test -- --run
  ```

- [ ] **Final type check**:
  ```bash
  npm run type-check
  ```

- [ ] **Run Playwright E2E tests**:
  ```bash
  npm run test:e2e
  ```
  Capture full output. If any E2E tests fail, document failures, assess if related to changes, fix if related, note if pre-existing.

- [ ] **Review commit history and create final commit** (if changes aren't committed):
  ```bash
  git log --oneline origin/develop..HEAD
  ```
  ```bash
  git add -A
  git commit -m "Fix #XXXX: [brief description]

  [More detailed description of the fix]

  - [Change 1]
  - [Change 2]
  - [Change 3]

  Closes #XXXX"
  ```

- [ ] **Push branch and trigger Vercel preview**:
  ```bash
  git push -u origin $(git branch --show-current)
  ```
  This automatically triggers a Vercel preview deployment.

- [ ] **Wait for Vercel preview deployment**: Poll until deployment reaches "Ready" state:
  ```bash
  npm run deploy-info
  ```
  Record the preview URL.

- [ ] **Verify preview site using Chrome DevTools MCP**: Navigate to the preview URL and verify:
  - Page loads without errors
  - The fix/feature works as expected
  - No visual regressions
  - Console has no errors

- [ ] **Write PR_DESCRIPTION.md**: Create `{{AUTORUN_FOLDER}}/PR_DESCRIPTION.md` with:
  - Summary (2-3 sentences)
  - Related issue reference (Closes #XXXX)
  - Changes made (categorized)
  - Implementation approach
  - Files changed table
  - Testing section (unit, type check, build, E2E, Vercel preview)
  - Checklist (coding conventions, TypeScript strict, React 19, Next.js App Router, accessibility)

  Keep it concise. Base all content on actual implementation files.

- [ ] **Create PR against `develop` branch** (NOT main):
  ```bash
  gh pr create --base develop --title "Fix #XXXX: [brief description]" --body-file {{AUTORUN_FOLDER}}/PR_DESCRIPTION.md
  ```
  Record the PR URL.

- [ ] **Write COMPLETION_SUMMARY.md**: Create `{{AUTORUN_FOLDER}}/COMPLETION_SUMMARY.md` with:
  - Issue details (number, title, URL)
  - Resolution status, branch, PR URL, date, loop count
  - Implementation statistics (phases, files, lines changed)
  - Testing summary (unit, E2E, type check, build)
  - Vercel preview URL and verification status
  - List of generated artifacts
  - Next steps (merge to develop → staging → verify → merge to main → production)

- [ ] **Confirm all artifacts exist**:
  - `ISSUE_CONTEXT.md`
  - `ISSUE_ANALYSIS.md`
  - `IMPLEMENTATION_PLAN.md`
  - `PHASE_*_IMPLEMENTATION.md` (one per phase)
  - `TEST_RESULTS.md`
  - `PR_DESCRIPTION.md`
  - `COMPLETION_SUMMARY.md`

  Report any missing files.

- [ ] **Verify PR created**:
  ```bash
  gh pr view
  ```

## Notes

- PRs are created against the `develop` branch, NOT main.
- Vercel preview deployments are triggered automatically on push.
