# Fabric Issue Solver Progress Gate

## Context

- **Playbook**: Fabric Issue Solver
- **Agent**: {{AGENT_NAME}}
- **Project**: {{AGENT_PATH}}
- **Loop**: {{LOOP_NUMBER}}
- **Date**: {{DATE}}
- **Working Folder**: {{AUTORUN_FOLDER}}

## Purpose

Verify the current phase implementation and run tests. This document has `resetOnCompletion: true` — Maestro automatically resets all tasks each loop iteration. The loop continuation decision is handled by `6_GATE.md`.

## Instructions

1. Read `{{AUTORUN_FOLDER}}/IMPLEMENTATION_PLAN.md` and find the `IN_PROGRESS` phase
2. Run tests and build verification
3. Update phase status and test results

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

## Notes

- Documents 4 and 5 have `resetOnCompletion: true` — Maestro resets them automatically each loop.
- Loop continuation and finalization are handled by `6_GATE.md` (non-reset).
- If a phase fails verification, it can be retried in the next loop iteration.
