# Document 2: Analyze Issue

## Context

- **Playbook**: Sylvan Issue Solver
- **Agent**: {{AGENT_NAME}}
- **Project**: {{AGENT_PATH}}
- **Loop**: {{LOOP_NUMBER}}
- **Date**: {{DATE}}
- **Working Folder**: {{AUTORUN_FOLDER}}

## Purpose

Perform deep analysis of the issue to understand root cause, affected files, and solution approach within Sylvan's Next.js architecture.

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

Based on the issue description, identify which Sylvan components are affected:

- [ ] **Search for related code**: Use the issue keywords to find relevant files:
  ```bash
  # Search for related terms in TypeScript/React files
  grep -r "keyword" --include="*.ts" --include="*.tsx" .
  ```

- [ ] **Map to Sylvan architecture**:

  | Area | Directory | Relevance |
  |------|-----------|-----------|
  | App Router Pages | `app/` | [Yes/No/Maybe] |
  | Layouts | `app/**/layout.tsx` | [Yes/No/Maybe] |
  | React Components | `components/` | [Yes/No/Maybe] |
  | Utility Functions | `lib/` | [Yes/No/Maybe] |
  | API Routes | `app/api/` | [Yes/No/Maybe] |
  | Styles | `styles/` or Tailwind | [Yes/No/Maybe] |
  | Static Assets | `public/` | [Yes/No/Maybe] |
  | Unit Tests | `__tests__/` | [Yes/No/Maybe] |
  | E2E Tests | `e2e/` | [Yes/No/Maybe] |
  | Configuration | `next.config.*`, `tailwind.config.*` | [Yes/No/Maybe] |

### Task 4: Understand Root Cause

- [ ] **For bugs**: Identify:
  - What is the expected behavior?
  - What is the actual behavior?
  - What code path causes the issue?
  - Can you reproduce it locally with `npm run dev`?

- [ ] **For features**: Identify:
  - What new functionality is requested?
  - How does it fit into the Next.js App Router architecture?
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
  - Does it affect SEO or metadata?
  - Does it require new npm packages?

- [ ] **Consider Next.js specifics**:
  - Server Components vs Client Components affected?
  - Static vs dynamic rendering implications?
  - Data fetching pattern changes needed?

### Task 6: Research Solutions

- [ ] **Look for prior art**:
  - Similar issues in the repo
  - Related PRs (merged or closed)
  - Comments from maintainers
  - Next.js/React documentation for patterns

- [ ] **Consider approaches**:
  - What are the possible solutions?
  - What are the trade-offs of each?
  - Which aligns best with Sylvan's patterns and Next.js best practices?

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
| `app/page.tsx` | [Modify/Create] | [What changes] |
| `components/Example.tsx` | [Modify/Create] | [What changes] |

### Secondary Files (may be affected)
| File | Reason |
|------|--------|
| `app/layout.tsx` | [Why it might need changes] |

### Test Files
| File | Status |
|------|--------|
| `__tests__/example.test.ts` | [Exists/Needs Creation/Needs Update] |
| `e2e/example.spec.ts` | [Exists/Needs Creation/Needs Update] |

## Dependencies
- **Imports affected code**: [List of files]
- **Configuration changes**: [Yes/No - details]
- **New packages needed**: [Yes/No - list if yes]
- **Documentation updates**: [Yes/No - details]

## Solution Approach

### Recommended Approach
[Description of the recommended solution]

### Alternative Approaches Considered
1. [Alternative 1]: [Why not chosen]
2. [Alternative 2]: [Why not chosen]

### Technical Details
[Specific implementation details, code patterns to follow, etc.]

### Next.js Considerations
- **Component Type**: [Server Component / Client Component / Both]
- **Rendering Strategy**: [Static / Dynamic / ISR]
- **Data Fetching**: [Server-side / Client-side / None]
- **SEO Impact**: [None / Metadata changes / Sitemap changes]

## Risk Assessment
- **Breaking Changes**: [Yes/No - details]
- **Performance Impact**: [None/Low/Medium/High - details]
- **Security Considerations**: [Any security implications]
- **Accessibility**: [Any a11y considerations]

## Testing Strategy
- **Unit Tests**: [What needs testing with Vitest]
- **E2E Tests**: [What needs testing with Playwright]
- **Manual Testing**: [Steps to verify fix with `npm run dev`]

## Analysis Complete
- **Date**: {{DATE}}
- **Ready for Planning**: Yes
```

## Success Criteria

- Root cause identified (for bugs) or requirements understood (for features)
- Affected files mapped to Sylvan's Next.js architecture
- Solution approach determined with Next.js considerations
- `ISSUE_ANALYSIS.md` created with all sections

## Status

Mark complete when analysis document is created.

---

**Next**: Document 3 will create a phased implementation plan.
