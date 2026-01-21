# Document 6: Finalize and Prepare PR

## Context

- **Playbook**: Sylvan Issue Solver
- **Agent**: {{AGENT_NAME}}
- **Project**: {{AGENT_PATH}}
- **Loop**: {{LOOP_NUMBER}}
- **Date**: {{DATE}}
- **Working Folder**: {{AUTORUN_FOLDER}}

## Purpose

Finalize the implementation, run E2E tests, push to trigger Vercel preview deployment, verify the preview, and create PR against the `develop` branch.

## Prerequisites

- All phases in `{{AUTORUN_FOLDER}}/IMPLEMENTATION_PLAN.md` are `COMPLETE`
- All unit tests pass (verified in Document 5)

## Tasks

### Task 1: Verify Completion

- [ ] **Check plan status**: Load `{{AUTORUN_FOLDER}}/IMPLEMENTATION_PLAN.md`
  - Confirm all phases are `COMPLETE`
  - If any phases are `PENDING` or `FAILED`, this document should not run

- [ ] **Final unit test run**:
  ```bash
  npm run test -- --run
  ```
  Confirm all tests still pass.

- [ ] **Final type check**:
  ```bash
  npm run type-check
  ```
  Confirm no type errors.

### Task 2: Run E2E Test Suite

- [ ] **Run Playwright E2E tests**:
  ```bash
  npm run test:e2e
  ```
  Capture full output.

- [ ] **Review E2E results**: If any E2E tests fail:
  - Document failures
  - Assess if they're related to the changes
  - Fix if related, note if pre-existing

### Task 3: Clean Up Commits

- [ ] **Review commit history**:
  ```bash
  git log --oneline origin/develop..HEAD
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

### Task 4: Push Branch and Trigger Vercel Preview

- [ ] **Push branch to remote**:
  ```bash
  git push -u origin $(git branch --show-current)
  ```
  This automatically triggers a Vercel preview deployment.

- [ ] **Check deployment status**:
  ```bash
  npm run deploy-info
  ```
  Note the preview URL and deployment status.

### Task 5: Wait for Vercel Preview Deployment

- [ ] **Monitor deployment status**: Poll until deployment reaches "Ready" state:
  ```bash
  # Check deployment status (repeat as needed)
  npm run deploy-info
  ```

- [ ] **Record preview URL**: Save the preview URL for verification
  - Format: `https://[branch-name]-[project].vercel.app` or similar

### Task 6: Verify Preview Site

- [ ] **Visual verification using Chrome DevTools MCP**: Navigate to the preview URL and verify:
  - Page loads without errors
  - The fix/feature works as expected
  - No visual regressions
  - Console has no errors

- [ ] **Document verification**: Note what was checked and confirmed

### Task 7: Create PR Description

- [ ] **Write PR_DESCRIPTION.md**: Create `{{AUTORUN_FOLDER}}/PR_DESCRIPTION.md`:

```markdown
# Pull Request: Fix #XXXX - [Issue Title]

## Summary

[2-3 sentence description of what this PR does]

## Related Issue

Closes #XXXX

## Changes Made

### [Category 1: e.g., Components]
- [Change description]
- [Change description]

### [Category 2: e.g., Pages]
- [Change description]

### [Category 3: e.g., Tests]
- [Change description]

## Implementation Approach

[Brief explanation of the solution approach and why it was chosen]

## Files Changed

| File | Change Type | Description |
|------|-------------|-------------|
| `app/page.tsx` | Modified | [Brief description] |
| `components/Example.tsx` | Added | [Brief description] |
| `__tests__/example.test.ts` | Modified | [Test updates] |

## Testing

### Automated Tests
- [x] Unit tests pass (`npm run test -- --run`)
- [x] Type checking passes (`npm run type-check`)
- [x] Build succeeds (`npm run build`)
- [x] E2E tests pass (`npm run test:e2e`)

### Vercel Preview
- **Preview URL**: [URL]
- **Status**: Ready
- **Verified**: [Date]

### Manual Testing
[Steps to manually verify the fix]

1. [Step 1]
2. [Step 2]
3. [Expected result]

## Checklist

- [ ] Code follows Sylvan coding conventions
- [ ] TypeScript strict mode compliance
- [ ] React 19 patterns followed
- [ ] Next.js App Router conventions
- [ ] Tests pass locally
- [ ] Vercel preview deployment verified
- [ ] No console errors on preview site
- [ ] Accessibility considerations addressed
- [ ] PR is focused (not 50+ files)

## Screenshots/Examples

[If applicable, add screenshots or example output]

## Additional Notes

[Any additional context for reviewers]

## Next Steps After Merge

1. Merge to `develop` → staging.sylvan.com deployment
2. Verify on staging site
3. Merge `develop` to `main` → production deployment
4. Verify on production

---

*PR prepared by Maestro Sylvan Issue Solver Playbook*
```

### Task 8: Create Pull Request

- [ ] **Create PR against `develop` branch** (NOT main):
  ```bash
  gh pr create --base develop --title "Fix #XXXX: [brief description]" --body-file {{AUTORUN_FOLDER}}/PR_DESCRIPTION.md
  ```

- [ ] **Record PR URL**: Save the PR URL for reference

### Task 9: Create Completion Summary

- [ ] **Write COMPLETION_SUMMARY.md**: Create `{{AUTORUN_FOLDER}}/COMPLETION_SUMMARY.md`:

```markdown
# Issue Resolution Summary

## Issue
- **Number**: #XXXX
- **Title**: [Title]
- **URL**: https://github.com/ksylvan/sylvan-website/issues/XXXX

## Resolution
- **Branch**: [branch name]
- **PR**: [PR URL]
- **Status**: COMPLETE
- **Date**: {{DATE}}
- **Total Loops**: {{LOOP_NUMBER}}

## Implementation Statistics
- **Phases Completed**: [N] of [N]
- **Files Modified**: [count]
- **Lines Changed**: [+X/-Y]
- **Tests**: All passing

## Files Changed
[List from git diff --stat]

## Key Changes
1. [Major change 1]
2. [Major change 2]
3. [Major change 3]

## Testing Summary
- **Unit Tests**: [Pass count] passing
- **E2E Tests**: [Pass count] passing
- **Type Check**: Passing
- **Build**: Successful

## Vercel Preview
- **Preview URL**: [URL]
- **Status**: Verified

## Generated Artifacts
- `ISSUE_CONTEXT.md` - Issue details
- `ISSUE_ANALYSIS.md` - Root cause analysis
- `IMPLEMENTATION_PLAN.md` - Phased plan
- `PHASE_N_IMPLEMENTATION.md` - Implementation records
- `TEST_RESULTS.md` - Test execution results
- `PR_DESCRIPTION.md` - Ready-to-use PR description
- `COMPLETION_SUMMARY.md` - This file

## Next Steps (Manual)

1. **Check Preview Site**
   - URL: [preview URL]
   - Verify functionality manually

2. **Review PR**
   - Check for JS/TS best practices
   - Verify React 19 patterns
   - Confirm Next.js conventions

3. **Merge to Develop**
   - Triggers staging.sylvan.com deployment

4. **Verify Staging**
   - Test on staging.sylvan.com

5. **Merge Develop to Main**
   - Triggers production deployment

6. **Verify Production**
   - Final verification on production site

## Playbook Complete
The Sylvan Issue Solver playbook has finished successfully.
```

### Task 10: Final Verification

- [ ] **Confirm all artifacts exist**:
  - [ ] `ISSUE_CONTEXT.md`
  - [ ] `ISSUE_ANALYSIS.md`
  - [ ] `IMPLEMENTATION_PLAN.md`
  - [ ] `PHASE_*_IMPLEMENTATION.md` (one per phase)
  - [ ] `TEST_RESULTS.md`
  - [ ] `PR_DESCRIPTION.md`
  - [ ] `COMPLETION_SUMMARY.md`

- [ ] **Verify PR created**:
  ```bash
  gh pr view
  ```

## Success Criteria

- All implementation complete
- E2E tests pass
- Branch pushed to remote
- Vercel preview deployment ready
- Preview site verified
- PR created against `develop` branch
- Completion summary created

## Status

Mark complete when all finalization artifacts are created and PR is submitted.

---

## Playbook Complete

**Generated Files:**
- `PR_DESCRIPTION.md` - Used to create the PR
- `COMPLETION_SUMMARY.md` - Reference for what was done

**Created:**
- Pull Request against `develop` branch
- Vercel preview deployment

**Next Steps:**
- Review PR
- Merge to develop (staging deployment)
- Verify on staging
- Merge develop to main (production deployment)
