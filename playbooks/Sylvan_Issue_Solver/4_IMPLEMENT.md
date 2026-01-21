# Document 4: Implement Phase

## Context

- **Playbook**: Sylvan Issue Solver
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
  cat path/to/file.tsx
  ```

- [ ] **Make the required changes following Sylvan patterns**:
  - **TypeScript**: Use strict mode compliance, proper type annotations
  - **React 19**: Use modern patterns (Server Components by default, 'use client' where needed)
  - **Next.js App Router**: Follow conventions for layouts, pages, loading states
  - **Biome/ESLint**: Follow code style (run `npm run lint` to check)
  - **Tailwind CSS**: Use utility classes, follow existing component patterns

- [ ] **Create new files** (if needed):
  - Follow Sylvan's file organization
  - Include proper TypeScript types
  - Add necessary imports
  - Use appropriate component type (Server vs Client)

- [ ] **Verify syntax and types**:
  ```bash
  npm run type-check
  npm run build
  ```
  Fix any TypeScript or build errors before proceeding.

- [ ] **Run unit tests**:
  ```bash
  npm run test -- --run
  ```
  Ensure all tests pass. Fix any failing tests before proceeding.

### Task 5: Document Changes

- [ ] **Create phase implementation record**: Write `{{AUTORUN_FOLDER}}/PHASE_{{LOOP_NUMBER}}_IMPLEMENTATION.md`:

```markdown
# Phase [N] Implementation Record

## Phase Information
- **Phase**: [N] - [Title]
- **Loop**: {{LOOP_NUMBER}}
- **Date**: {{DATE}}

## Changes Made

### File: `app/example/page.tsx`
**Action**: [Created/Modified/Deleted]

**Changes**:
- [Description of change 1]
- [Description of change 2]

**Key Code Added/Modified**:
```tsx
// Relevant code snippet
```

### File: `components/Example.tsx`
**Action**: [Created/Modified/Deleted]

**Changes**:
- [Description of changes]

## Implementation Notes
[Any important notes about the implementation]

## Sylvan Patterns Followed
- [ ] TypeScript strict mode compliance
- [ ] React 19 patterns (Server Components, etc.)
- [ ] Next.js App Router conventions
- [ ] Tailwind CSS utility classes
- [ ] Biome/ESLint code style

## Deviations from Plan
[Any changes from the original plan and why]

## Verification Performed
```bash
# Commands run
npm run type-check
npm run build
```

**Result**: [Pass/Fail]

## Ready for Testing
- [ ] All planned changes complete
- [ ] Code compiles without errors
- [ ] Type checking passes
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
- TypeScript compiles without errors (`npm run type-check`)
- Build succeeds (`npm run build`)
- Changes documented in `PHASE_N_IMPLEMENTATION.md`
- Plan updated with current status

## Status

Mark complete when phase implementation is done (pending verification).

---

**Next**: Document 5 will verify the implementation and determine if more phases remain.
