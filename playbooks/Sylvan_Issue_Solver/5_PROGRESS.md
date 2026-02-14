# Sylvan Issue Solver Progress Gate

## Context

- **Playbook**: Sylvan Issue Solver
- **Agent**: {{AGENT_NAME}}
- **Project**: {{AGENT_PATH}}
- **Loop**: {{LOOP_NUMBER}}
- **Date**: {{DATE}}
- **Working Folder**: {{AUTORUN_FOLDER}}

## Purpose

Verify the current phase implementation, run tests, and determine if more phases remain. **This is the only document with Reset ON** — it controls loop continuation by resetting tasks in documents 4-5 when more phases need implementation.

## Instructions

1. Read `{{AUTORUN_FOLDER}}/IMPLEMENTATION_PLAN.md` and find the `IN_PROGRESS` phase
2. Run unit tests, type checking, and build verification
3. Update phase status and test results
4. Check for remaining PENDING phases
5. If work remains → Reset documents 4-5 to continue
6. If no work remains → Run E2E tests, deploy preview, create PR

## Prerequisites

- `{{AUTORUN_FOLDER}}/IMPLEMENTATION_PLAN.md` exists
- A phase is marked as `IN_PROGRESS`

## Task 1: Load Current State

- [ ] **Read plan**: Load `{{AUTORUN_FOLDER}}/IMPLEMENTATION_PLAN.md`

- [ ] **Identify current phase**: Find the phase marked `IN_PROGRESS`

- [ ] **Load phase implementation**: Read the corresponding `PHASE_N_IMPLEMENTATION.md`

## Task 2: Run Unit Test Suite

- [ ] **Run all unit tests**:
  ```bash
  npm run test -- --run
  ```
  Capture full output.

- [ ] **Run verbose tests** (if failures):
  ```bash
  npm run test -- --run --reporter=verbose
  ```

## Task 3: Verify Type Checking

- [ ] **Run TypeScript type check**:
  ```bash
  npm run type-check
  ```
  Ensure no type errors.

## Task 4: Verify Build

- [ ] **Run production build**:
  ```bash
  npm run build
  ```
  Ensure build completes successfully.

## Task 5: Check Success Criteria

- [ ] **Review phase success criteria**: From the plan, check each criterion:
  - [ ] Criterion 1: [Pass/Fail]
  - [ ] Criterion 2: [Pass/Fail]
  - [ ] ...

- [ ] **Run phase verification commands**: Execute the verification commands from the plan

## Task 6: Update Test Results

- [ ] **Write/Update TEST_RESULTS.md**: Create or append to `{{AUTORUN_FOLDER}}/TEST_RESULTS.md` with the current loop's unit test results, type check results, build verification, phase success criteria, and overall verification status.

## Task 7: Update Phase Status

Based on verification results:

- [ ] **If all tests pass AND criteria met**:
  - Update phase status in `IMPLEMENTATION_PLAN.md` to `COMPLETE`
  - Set `Completed` date
  - Commit the changes:
    ```bash
    git add -A
    git commit -m "Complete Phase N: [brief description]

    Issue: #XXXX
    Phase: N of M - COMPLETE
    Tests: All passing"
    ```

- [ ] **If tests fail OR criteria not met**:
  - Keep phase status as `IN_PROGRESS` or set to `FAILED`
  - Document the failures in TEST_RESULTS.md
  - Note what needs to be fixed

## Task 8: Progress Gate Check

- [ ] **Check for remaining work**: Read `{{AUTORUN_FOLDER}}/IMPLEMENTATION_PLAN.md` and count phases by status. The loop should CONTINUE (reset docs 4-5) if there are phases with status `PENDING`. The loop should EXIT (do NOT reset) when no `PENDING` phases remain (all are COMPLETE, FAILED, or BLOCKED).

  Display a summary: (1) COMPLETE count, (2) PENDING count, (3) FAILED/BLOCKED count, (4) decision.

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
- **Next Action**: [Continue to Phase N / Proceed to Finalization / Blocked]
```

## Reset Tasks (Only if work remains)

If the progress gate check above determines we need to continue (PENDING phases remain), reset all tasks in the following documents:

- [ ] **Reset 4_IMPLEMENT.md**: Uncheck all tasks in `{{AUTORUN_FOLDER}}/4_IMPLEMENT.md`
- [ ] **Reset 5_PROGRESS.md**: Uncheck all tasks in `{{AUTORUN_FOLDER}}/5_PROGRESS.md`

**IMPORTANT**: Only reset documents 4-5 if there are PENDING phases remaining. If all phases are COMPLETE, FAILED, or BLOCKED, leave these reset tasks unchecked to allow the pipeline to exit to the finalization tasks below.

## Decision Logic

```
IF IMPLEMENTATION_PLAN.md doesn't exist:
    → Do NOT reset anything (PIPELINE JUST STARTED - LET IT RUN)

ELSE IF phases with status `PENDING` exist:
    → Reset documents 4-5 (CONTINUE TO NEXT PHASE)

ELSE:
    → Do NOT reset anything (ALL PHASES DONE - EXIT AND FINALIZE)
```

## How This Works

This document controls loop continuation through resets:
- **Reset tasks checked** → Documents 4-5 get reset → Loop continues
- **Reset tasks unchecked** → Nothing gets reset → Pipeline exits after finalization below

## Finalization (Only when exiting — all phases COMPLETE)

The following tasks run ONLY when the loop is exiting (no reset tasks checked above). They run E2E tests, deploy a preview, and create the PR.

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

## Manual Override

**To force exit early:**
- Leave all reset tasks unchecked regardless of remaining PENDING phases
- The finalization tasks below will still run

**To continue past natural exit:**
- Check the reset tasks even when no PENDING phases remain
- Useful if you want to retry a FAILED phase

## Notes

- Reset tasks checked → Documents 4-5 reset → Loop continues.
- Reset tasks unchecked → Nothing resets → Finalization runs → Pipeline exits.
- If a phase fails verification, it can be retried in the next loop iteration.
- PRs are created against the `develop` branch, NOT main.
- Vercel preview deployments are triggered automatically on push.
