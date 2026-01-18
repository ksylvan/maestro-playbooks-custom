# Document 4: Implement Phase

## Context

- **Playbook**: Fabric Issue Solver
- **Agent**: {{AGENT_NAME}}
- **Project**: {{AGENT_PATH}}
- **Loop**: {{LOOP_NUMBER}}
- **Date**: {{DATE}}
- **Working Folder**: {{AUTORUN_FOLDER}}

## Purpose

Execute the next incomplete phase from the implementation plan.

## Prerequisites

- `{{AUTORUN_FOLDER}}/IMPLEMENTATION_PLAN.md` exists from Document 3
- At least one phase has status `PENDING`

## Tasks

### Task 1: Load Implementation Plan

- [ ] **Read plan**: Load `{{AUTORUN_FOLDER}}/IMPLEMENTATION_PLAN.md`

- [ ] **Find next phase**: Identify the first phase with status `PENDING`
  - If no `PENDING` phases exist, all implementation is complete
  - Skip to Task 7 and note "All phases complete"

- [ ] **Record current phase**: Note the phase number and title being implemented

### Task 2: Update Phase Status

- [ ] **Mark phase as IN_PROGRESS**: Update `IMPLEMENTATION_PLAN.md`:
  - Change phase status from `PENDING` to `IN_PROGRESS`
  - Set `Started` date to {{DATE}}

### Task 3: Review Phase Requirements

- [ ] **Load phase details**: From the plan, extract:
  - Objective
  - Files to modify
  - Implementation steps
  - Success criteria
  - Verification commands

- [ ] **Load supporting context**: Read:
  - `{{AUTORUN_FOLDER}}/ISSUE_CONTEXT.md` for background
  - `{{AUTORUN_FOLDER}}/ISSUE_ANALYSIS.md` for technical details
  - Any `PHASE_N_IMPLEMENTATION.md` from prior phases

### Task 4: Execute Implementation

For each file in the phase:

- [ ] **Read existing file** (if modifying):
  ```bash
  cat path/to/file.go
  ```

- [ ] **Make the required changes**:
  - Follow Fabric's coding patterns (see CLAUDE.md)
  - Use `pkg/errors` for error wrapping
  - Propagate `context.Context` properly
  - Follow Go idioms and naming conventions

- [ ] **Create new files** (if needed):
  - Follow Fabric's file organization
  - Include proper package declaration
  - Add necessary imports

- [ ] **Verify syntax**:
  ```bash
  go build ./...
  ```
  Fix any compilation errors before proceeding.

### Task 5: Document Changes

- [ ] **Create phase implementation record**: Write `{{AUTORUN_FOLDER}}/PHASE_{{LOOP_NUMBER}}_IMPLEMENTATION.md`:

```markdown
# Phase [N] Implementation Record

## Phase Information
- **Phase**: [N] - [Title]
- **Loop**: {{LOOP_NUMBER}}
- **Date**: {{DATE}}

## Changes Made

### File: `path/to/file.go`
**Action**: [Created/Modified/Deleted]

**Changes**:
- [Description of change 1]
- [Description of change 2]

**Key Code Added/Modified**:
```go
// Relevant code snippet
```

### File: `path/to/another_file.go`
**Action**: [Created/Modified/Deleted]

**Changes**:
- [Description of changes]

## Implementation Notes
[Any important notes about the implementation]

## Deviations from Plan
[Any changes from the original plan and why]

## Verification Performed
```bash
# Commands run
go build ./...
```

**Result**: [Pass/Fail]

## Ready for Testing
- [ ] All planned changes complete
- [ ] Code compiles without errors
- [ ] Ready for test verification
```

### Task 6: Stage Changes

- [ ] **Stage modified files**:
  ```bash
  git add [files modified in this phase]
  ```

- [ ] **Verify staged changes**:
  ```bash
  git diff --cached --stat
  ```

- [ ] **Create work-in-progress commit** (optional but recommended):
  ```bash
  git commit -m "WIP: Phase N - [brief description]

  Issue: #XXXX
  Phase: N of M
  Status: Pending verification"
  ```

### Task 7: Update Plan Status

- [ ] **Update IMPLEMENTATION_PLAN.md**:
  - If implementation successful: Leave status as `IN_PROGRESS` (verification pending)
  - If implementation failed: Set status to `FAILED` with notes
  - Update the Progress Tracking table
  - Update `Last Updated` timestamp

## Success Criteria

- Next pending phase identified and started
- All planned changes for the phase implemented
- Code compiles without errors
- Changes documented in `PHASE_N_IMPLEMENTATION.md`
- Plan updated with current status

## Status

Mark complete when phase implementation is done (pending verification).

---

**Next**: Document 5 will verify the implementation and determine if more phases remain.
