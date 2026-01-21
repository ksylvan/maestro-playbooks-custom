# Document 3: Create Implementation Plan

## Context

- **Playbook**: Sylvan Issue Solver
- **Agent**: {{AGENT_NAME}}
- **Project**: {{AGENT_PATH}}
- **Loop**: {{LOOP_NUMBER}}
- **Date**: {{DATE}}
- **Working Folder**: {{AUTORUN_FOLDER}}

## Purpose

Create a detailed, phased implementation plan that can be executed iteratively.

## Prerequisites

- `{{AUTORUN_FOLDER}}/ISSUE_CONTEXT.md` exists from Document 1
- `{{AUTORUN_FOLDER}}/ISSUE_ANALYSIS.md` exists from Document 2

## Tasks

### Task 1: Check for Existing Plan (Loop Awareness)

- [ ] **Check if plan exists**: Look for `{{AUTORUN_FOLDER}}/IMPLEMENTATION_PLAN.md`
  - If it exists AND has valid phases, skip to Success Criteria (already planned)
  - If it doesn't exist or is incomplete, continue with Task 2

### Task 2: Load Analysis

- [ ] **Read analysis**: Load `{{AUTORUN_FOLDER}}/ISSUE_ANALYSIS.md`
- [ ] **Extract key information**:
  - Affected files list
  - Solution approach
  - Testing strategy
  - Dependencies

### Task 3: Design Phase Structure

Break the implementation into logical, atomic phases. Each phase should:
- Be completable in one iteration
- Have clear success criteria
- Be independently testable where possible
- Build on previous phases

- [ ] **Identify natural breakpoints**:
  - Core implementation vs. peripheral changes
  - Interface changes vs. implementation
  - Code changes vs. test changes
  - Changes that can be verified independently

### Task 4: Define Each Phase

For each phase, define:

- [ ] **Phase scope**: What specific changes are included
- [ ] **Files to modify**: Exact files touched in this phase
- [ ] **Dependencies**: What must be complete before this phase
- [ ] **Verification**: How to confirm phase is successful
- [ ] **Rollback plan**: How to undo if needed

### Task 5: Order Phases

- [ ] **Establish sequence**: Order phases by:
  1. Foundation/infrastructure changes first
  2. Core functionality second
  3. Integration/wiring third
  4. Tests fourth (or alongside each phase)
  5. Documentation/cleanup last

- [ ] **Check dependencies**: Ensure no phase depends on a later phase

### Task 6: Create Plan Document

- [ ] **Write IMPLEMENTATION_PLAN.md**: Create `{{AUTORUN_FOLDER}}/IMPLEMENTATION_PLAN.md`:

```markdown
# Implementation Plan

## Issue Reference
- **Issue**: #XXXX - [Title]
- **Branch**: [current branch name]
- **Created**: {{DATE}}

## Plan Overview
[2-3 sentence summary of the implementation approach]

## Total Phases: N

---

## Phase 1: [Phase Title]

### Status: PENDING

### Objective
[What this phase accomplishes]

### Files to Modify
| File | Action | Description |
|------|--------|-------------|
| `app/page.tsx` | [Create/Modify/Delete] | [What changes] |

### Implementation Steps
1. [Step 1]
2. [Step 2]
3. [Step 3]

### Success Criteria
- [ ] [Criterion 1]
- [ ] [Criterion 2]

### Verification
```bash
# Commands to verify this phase
npm run type-check
npm run test -- --run
npm run build
```

### Dependencies
- None (or list prior phases)

---

## Phase 2: [Phase Title]

### Status: PENDING

### Objective
[What this phase accomplishes]

### Files to Modify

| File | Action | Description |
|------|--------|-------------|
| `components/Example.tsx` | [Create/Modify/Delete] | [What changes] |

### Implementation Steps
1. [Step 1]
2. [Step 2]

### Success Criteria
- [ ] [Criterion 1]
- [ ] [Criterion 2]

### Verification
```bash
# Commands to verify this phase
npm run type-check
npm run test -- --run
```

### Dependencies
- Phase 1 complete

---

## Phase N: [Final Phase - Usually Tests/Cleanup]

### Status: PENDING

### Objective
[What this phase accomplishes]

### Files to Modify

| File | Action | Description |
|------|--------|-------------|
| `__tests__/example.test.ts` | [Create/Modify] | [Test coverage] |

### Implementation Steps
1. [Step 1]
2. [Step 2]

### Success Criteria
- [ ] All unit tests pass
- [ ] Type checking passes
- [ ] Build succeeds

### Verification
```bash
npm run type-check
npm run test -- --run
npm run build
```

### Dependencies
- All prior phases complete

---

## Progress Tracking

| Phase | Title | Status | Started | Completed |
|-------|-------|--------|---------|-----------|
| 1 | [Title] | PENDING | - | - |
| 2 | [Title] | PENDING | - | - |
| N | [Title] | PENDING | - | - |

## Status Values
- `PENDING` - Not yet started
- `IN_PROGRESS` - Currently being implemented
- `COMPLETE` - Finished and verified
- `FAILED` - Implementation failed, needs retry
- `BLOCKED` - Cannot proceed, needs intervention

## Plan Metadata
- **Total Phases**: N
- **Estimated Complexity**: [Low/Medium/High]
- **Plan Created**: {{DATE}}
- **Last Updated**: {{DATE}}
```

### Task 7: Validate Plan

- [ ] **Check completeness**:
  - All affected files from analysis are covered
  - No circular dependencies between phases
  - Each phase has clear success criteria
  - Verification commands are valid for Sylvan (`npm run test -- --run`, `npm run type-check`, `npm run build`)

- [ ] **Check feasibility**:
  - Phases are appropriately sized
  - No phase is too large to complete in one iteration
  - Dependencies are realistic

## Success Criteria

- Implementation broken into logical phases
- Each phase has clear scope and success criteria
- Dependencies correctly ordered
- `IMPLEMENTATION_PLAN.md` created with all phases

## Status

Mark complete when plan document is created.

---

**Next**: Document 4 will begin implementing phases (this is where the loop starts).
