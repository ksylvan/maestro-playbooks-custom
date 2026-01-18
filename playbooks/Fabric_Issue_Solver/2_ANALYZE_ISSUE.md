# Document 2: Analyze Issue

## Context

- **Playbook**: Fabric Issue Solver
- **Agent**: {{AGENT_NAME}}
- **Project**: {{AGENT_PATH}}
- **Loop**: {{LOOP_NUMBER}}
- **Date**: {{DATE}}
- **Working Folder**: {{AUTORUN_FOLDER}}

## Purpose

Perform deep analysis of the issue to understand root cause, affected files, and solution approach.

## Prerequisites

- `{{AUTORUN_FOLDER}}/ISSUE_CONTEXT.md` exists from Document 1
- No `{{AUTORUN_FOLDER}}/BRANCH_ERROR.md` (branch safety passed)

## Tasks

### Task 1: Check for Existing Analysis (Loop Awareness)

- [ ] **Check if analysis exists**: Look for `{{AUTORUN_FOLDER}}/ISSUE_ANALYSIS.md`
  - If it exists AND has valid content, skip to Success Criteria (already analyzed)
  - If it doesn't exist or is empty, continue with Task 2

### Task 2: Load Issue Context

- [ ] **Read context**: Load `{{AUTORUN_FOLDER}}/ISSUE_CONTEXT.md`
- [ ] **Extract key details**:
  - Issue type (bug/feature/enhancement)
  - Issue description and requirements
  - Any error messages or reproduction steps

### Task 3: Identify Affected Code Areas

Based on the issue description, identify which Fabric components are affected:

- [ ] **Search for related code**: Use the issue keywords to find relevant files:
  ```bash
  # Search for related terms in Go files
  grep -r "keyword" --include="*.go" .
  ```

- [ ] **Map to Fabric architecture**:

  | Area | Directory | Relevance |
  |------|-----------|-----------|
  | CLI | `internal/cli/` | [Yes/No/Maybe] |
  | Core Chat | `internal/core/` | [Yes/No/Maybe] |
  | AI Plugins | `internal/plugins/ai/` | [Yes/No/Maybe] |
  | DB Plugins | `internal/plugins/db/` | [Yes/No/Maybe] |
  | Strategies | `internal/plugins/strategy/` | [Yes/No/Maybe] |
  | Server | `internal/server/` | [Yes/No/Maybe] |
  | Tools | `internal/tools/` | [Yes/No/Maybe] |
  | Patterns | `data/patterns/` | [Yes/No/Maybe] |
  | Strategies | `data/strategies/` | [Yes/No/Maybe] |

### Task 4: Understand Root Cause

- [ ] **For bugs**: Identify:
  - What is the expected behavior?
  - What is the actual behavior?
  - What code path causes the issue?
  - Can you reproduce it locally?

- [ ] **For features**: Identify:
  - What new functionality is requested?
  - How does it fit into existing architecture?
  - What existing patterns can we follow?
  - Are there similar implementations to reference?

- [ ] **Read relevant source files**: Examine the files identified in Task 3
  - Understand current implementation
  - Identify integration points
  - Note any related tests

### Task 5: Assess Impact and Dependencies

- [ ] **Check dependencies**:
  - What files import/use the affected code?
  - What tests cover the affected code?
  - Are there configuration changes needed?

- [ ] **Evaluate scope**:
  - Is this a localized fix or wide-reaching change?
  - Does it require API/interface changes?
  - Does it affect backward compatibility?

### Task 6: Research Solutions

- [ ] **Look for prior art**:
  - Similar issues in the repo
  - Related PRs (merged or closed)
  - Comments from maintainers

- [ ] **Consider approaches**:
  - What are the possible solutions?
  - What are the trade-offs of each?
  - Which aligns best with Fabric's patterns?

### Task 7: Create Analysis Document

- [ ] **Write ISSUE_ANALYSIS.md**: Create `{{AUTORUN_FOLDER}}/ISSUE_ANALYSIS.md`:

```markdown
# Issue Analysis

## Issue Reference
- **Issue**: #XXXX - [Title]
- **Type**: [Bug / Feature / Enhancement]
- **Complexity**: [Low / Medium / High]

## Problem Statement
[Clear, concise description of what needs to be solved]

## Root Cause Analysis

### For Bugs
- **Expected Behavior**: [What should happen]
- **Actual Behavior**: [What currently happens]
- **Root Cause**: [Why it happens]
- **Reproduction Steps**: [How to reproduce]

### For Features
- **Requirement**: [What is being requested]
- **Use Case**: [Why it's needed]
- **User Impact**: [Who benefits and how]

## Affected Files

### Primary Files (will be modified)
| File | Change Type | Description |
|------|-------------|-------------|
| `path/to/file.go` | [Modify/Create] | [What changes] |

### Secondary Files (may be affected)
| File | Reason |
|------|--------|
| `path/to/file.go` | [Why it might need changes] |

### Test Files
| File | Status |
|------|--------|
| `path/to/file_test.go` | [Exists/Needs Creation/Needs Update] |

## Dependencies
- **Imports affected code**: [List of files]
- **Configuration changes**: [Yes/No - details]
- **Documentation updates**: [Yes/No - details]

## Solution Approach

### Recommended Approach
[Description of the recommended solution]

### Alternative Approaches Considered
1. [Alternative 1]: [Why not chosen]
2. [Alternative 2]: [Why not chosen]

### Technical Details
[Specific implementation details, code patterns to follow, etc.]

## Risk Assessment
- **Breaking Changes**: [Yes/No - details]
- **Performance Impact**: [None/Low/Medium/High - details]
- **Security Considerations**: [Any security implications]

## Testing Strategy
- **Unit Tests**: [What needs testing]
- **Integration Tests**: [If applicable]
- **Manual Testing**: [Steps to verify fix]

## Analysis Complete
- **Date**: {{DATE}}
- **Ready for Planning**: Yes
```

## Success Criteria

- Root cause identified (for bugs) or requirements understood (for features)
- Affected files mapped
- Solution approach determined
- `ISSUE_ANALYSIS.md` created with all sections

## Status

Mark complete when analysis document is created.

---

**Next**: Document 3 will create a phased implementation plan.
