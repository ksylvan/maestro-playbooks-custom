# Document 5: Verify Progress (Loop Gate)

## Context

- **Playbook**: Fabric Issue Solver
- **Agent**: {{AGENT_NAME}}
- **Project**: {{AGENT_PATH}}
- **Loop**: {{LOOP_NUMBER}}
- **Date**: {{DATE}}
- **Working Folder**: {{AUTORUN_FOLDER}}

## Purpose

Verify the current phase implementation, run tests, and determine if more phases remain.

**This is the LOOP GATE document** - it controls whether the playbook continues to the next phase or proceeds to finalization.

## Prerequisites

- `{{AUTORUN_FOLDER}}/IMPLEMENTATION_PLAN.md` exists
- A phase is marked as `IN_PROGRESS`

## Tasks

### Task 1: Load Current State

- [ ] **Read plan**: Load `{{AUTORUN_FOLDER}}/IMPLEMENTATION_PLAN.md`

- [ ] **Identify current phase**: Find the phase marked `IN_PROGRESS`

- [ ] **Load phase implementation**: Read the corresponding `PHASE_N_IMPLEMENTATION.md`

### Task 2: Run Test Suite

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

### Task 3: Verify Build

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

### Task 4: Check Success Criteria

- [ ] **Review phase success criteria**: From the plan, check each criterion:
  - [ ] Criterion 1: [Pass/Fail]
  - [ ] Criterion 2: [Pass/Fail]
  - [ ] ...

- [ ] **Run phase verification commands**: Execute the verification commands from the plan

### Task 5: Update Test Results

- [ ] **Write/Update TEST_RESULTS.md**: Create `{{AUTORUN_FOLDER}}/TEST_RESULTS.md`:

```markdown
# Test Results

## Latest Run
- **Date**: {{DATE}}
- **Loop**: {{LOOP_NUMBER}}
- **Phase Verified**: [N] - [Title]

## Test Execution

### Full Test Suite
```
[Output from go test ./...]
```

### Summary
- **Total Tests**: X
- **Passed**: X
- **Failed**: X
- **Skipped**: X

### Failed Tests
[List any failed tests with details, or "None"]

## Build Verification
- **Main Binary**: [Pass/Fail]
- **Helper Tools**: [Pass/Fail/N/A]

## Phase Success Criteria
| Criterion | Status |
|-----------|--------|
| [Criterion 1] | [Pass/Fail] |
| [Criterion 2] | [Pass/Fail] |

## Overall Verification
- **Phase Status**: [VERIFIED / FAILED]
- **Ready to Continue**: [Yes/No]
```

### Task 6: Update Phase Status

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

### Task 7: Determine Next Action

- [ ] **Count remaining phases**: Check `IMPLEMENTATION_PLAN.md` for phases with status `PENDING`

- [ ] **Decision point**:

  **If PENDING phases remain**:
  - Log: "Phase [N] complete. [M] phases remaining. Continuing to next phase."
  - The loop will continue (this document has `resetOnCompletion: true`)
  - Documents 4-5 will run again for the next phase

  **If NO PENDING phases remain (all COMPLETE)**:
  - Log: "All phases complete. Proceeding to finalization."
  - Update `IMPLEMENTATION_PLAN.md` with final status
  - The playbook will proceed to Document 6

  **If phase FAILED and cannot proceed**:
  - Log: "Phase [N] failed verification. Intervention required."
  - Set phase status to `BLOCKED`
  - Document the blocker

### Task 8: Loop Status Summary

- [ ] **Update IMPLEMENTATION_PLAN.md** Progress Tracking table:

```markdown
## Progress Tracking

| Phase | Title | Status | Started | Completed |
|-------|-------|--------|---------|-----------|
| 1 | [Title] | COMPLETE | [date] | [date] |
| 2 | [Title] | COMPLETE | [date] | [date] |
| 3 | [Title] | PENDING | - | - |
| ... | ... | ... | ... | ... |

## Loop Summary
- **Current Loop**: {{LOOP_NUMBER}}
- **Phases Completed**: X of Y
- **Next Action**: [Continue to Phase N / Proceed to Finalization / Blocked]
```

## Loop Gate Logic

```
IF current phase verification = PASSED
   AND remaining PENDING phases > 0
THEN
   → Continue looping (reset to Document 4)

IF current phase verification = PASSED
   AND remaining PENDING phases = 0
THEN
   → Exit loop, proceed to Document 6 (Finalization)

IF current phase verification = FAILED
THEN
   → Mark as FAILED/BLOCKED
   → Continue looping to retry OR exit with error
```

## Success Criteria

- Current phase verified (tests pass, criteria met)
- Phase status updated in plan
- Test results documented
- Next action determined
- Loop continues or proceeds to finalization

## Status

Mark complete when verification is done and next action is determined.

---

**Next**:
- If more phases: Loop back to Document 4
- If all complete: Document 6 will prepare PR content
