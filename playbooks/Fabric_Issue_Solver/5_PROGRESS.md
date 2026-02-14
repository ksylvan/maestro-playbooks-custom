# Fabric Issue Solver Progress Gate

## Context

- **Playbook**: Fabric Issue Solver
- **Agent**: {{AGENT_NAME}}
- **Project**: {{AGENT_PATH}}
- **Loop**: {{LOOP_NUMBER}}
- **Date**: {{DATE}}
- **Working Folder**: {{AUTORUN_FOLDER}}

## Purpose

Verify the current phase implementation, run tests, and determine if more phases remain. **This is the only document with Reset ON** — it controls loop continuation by resetting tasks in documents 4-5 when more phases need implementation.

## Instructions

1. Read `{{AUTORUN_FOLDER}}/IMPLEMENTATION_PLAN.md` and find the `IN_PROGRESS` phase
2. Run tests and build verification
3. Update phase status and test results
4. Check for remaining PENDING phases
5. If work remains → Reset documents 4-5 to continue
6. If no work remains → Finalize and prepare PR

## Prerequisites

- `{{AUTORUN_FOLDER}}/IMPLEMENTATION_PLAN.md` exists
- A phase is marked as `IN_PROGRESS`

## Task 1: Load Current State

- [ ] **Read plan**: Load `{{AUTORUN_FOLDER}}/IMPLEMENTATION_PLAN.md`

- [ ] **Identify current phase**: Find the phase marked `IN_PROGRESS`

- [ ] **Load phase implementation**: Read the corresponding `PHASE_N_IMPLEMENTATION.md`

## Task 2: Run Test Suite

- [ ] **Run all tests**:
  ```bash
  go test ./...
  ```
  Capture full output.

- [ ] **Run verbose tests** (if failures):
  ```bash
  go test -v ./...
  ```

- [ ] **Check specific package tests** (if applicable):
  ```bash
  go test -v ./internal/[affected_package]
  ```

## Task 3: Verify Build

- [ ] **Clean build**:
  ```bash
  go build -o fabric ./cmd/fabric
  ```

- [ ] **Build helper tools** (if affected):
  ```bash
  go build -o code2context ./cmd/code2context
  go build -o to_pdf ./cmd/to_pdf
  go build -o generate_changelog ./cmd/generate_changelog
  ```

## Task 4: Check Success Criteria

- [ ] **Review phase success criteria**: From the plan, check each criterion:
  - [ ] Criterion 1: [Pass/Fail]
  - [ ] Criterion 2: [Pass/Fail]
  - [ ] ...

- [ ] **Run phase verification commands**: Execute the verification commands from the plan

## Task 5: Update Test Results

- [ ] **Write/Update TEST_RESULTS.md**: Create or append to `{{AUTORUN_FOLDER}}/TEST_RESULTS.md` with the current loop's test results, build verification, phase success criteria, and overall verification status.

## Task 6: Update Phase Status

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

## Task 7: Progress Gate Check

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

The following tasks run ONLY when the loop is exiting (no reset tasks checked above). They finalize the implementation and prepare the PR.

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
- The changelog is generated using the project's `generate_changelog` tool after PR creation.
