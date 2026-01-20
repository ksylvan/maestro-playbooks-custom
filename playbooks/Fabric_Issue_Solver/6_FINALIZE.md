# Document 6: Finalize and Prepare PR

## Context

- **Playbook**: Fabric Issue Solver
- **Agent**: {{AGENT_NAME}}
- **Project**: {{AGENT_PATH}}
- **Loop**: {{LOOP_NUMBER}}
- **Date**: {{DATE}}
- **Working Folder**: {{AUTORUN_FOLDER}}

## Purpose

Finalize the implementation, generate changelog, and prepare PR content for submission.

## Prerequisites

- All phases in `{{AUTORUN_FOLDER}}/IMPLEMENTATION_PLAN.md` are `COMPLETE`
- All tests pass (verified in Document 5)

## Tasks

### Task 1: Verify Completion

- [ ] **Check plan status**: Load `{{AUTORUN_FOLDER}}/IMPLEMENTATION_PLAN.md`
  - Confirm all phases are `COMPLETE`
  - If any phases are `PENDING` or `FAILED`, this document should not run

- [ ] **Final test run**:
  ```bash
  go test ./...
  ```
  Confirm all tests still pass.

### Task 2: Clean Up Commits

- [ ] **Review commit history**:
  ```bash
  git log --oneline origin/main..HEAD
  ```

- [ ] **Squash WIP commits** (if desired):
  ```bash
  # Interactive rebase to clean up history
  git rebase -i origin/main
  ```

  Or leave as-is if commit history tells a good story.

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

### Task 3: Gather Implementation Summary

- [ ] **Collect all changes**: Review all `PHASE_N_IMPLEMENTATION.md` files

- [ ] **List all modified files**:
  ```bash
  git diff --stat origin/main..HEAD
  ```

- [ ] **Summarize the solution**: Consolidate the approach taken

### Task 4: Create PR Description

- [ ] **Write PR_DESCRIPTION.md**: Create `{{AUTORUN_FOLDER}}/PR_DESCRIPTION.md`:

```markdown
# Pull Request: Fix #XXXX - [Issue Title]

## Summary

[2-3 sentence description of what this PR does]

## Related Issue

Closes #XXXX

## Changes Made

### [Category 1: e.g., Core Changes]
- [Change description]
- [Change description]

### [Category 2: e.g., Tests]
- [Change description]

### [Category 3: e.g., Documentation]
- [Change description]

## Implementation Approach

[Brief explanation of the solution approach and why it was chosen]

## Files Changed

| File | Change Type | Description |
|------|-------------|-------------|
| `path/to/file.go` | Modified | [Brief description] |
| `path/to/new_file.go` | Added | [Brief description] |
| `path/to/test_file.go` | Modified | [Test updates] |

## Testing

### Automated Tests
- [ ] All existing tests pass
- [ ] New tests added for [describe coverage]

### Manual Testing
[Steps to manually verify the fix]

1. [Step 1]
2. [Step 2]
3. [Expected result]

## Checklist

- [ ] Code follows Fabric coding conventions
- [ ] Tests pass locally (`go test ./...`)
- [ ] Changelog generated
- [ ] No hardcoded secrets or credentials
- [ ] PR is focused (not 50+ files)
- [ ] Error handling uses `pkg/errors`
- [ ] Context propagation is correct

## Screenshots/Examples

[If applicable, add screenshots or example output]

## Additional Notes

[Any additional context for reviewers]

---

*PR prepared by Maestro Fabric Issue Solver Playbook*
```

### Task 5: Use the information to create the PR

To create the Pull Request:

- Use content from `PR_DESCRIPTION.md`
- Reference issue #XXXX
- Request review from maintainers

- [ ] Create the pull request and save its number (referred to in the rest of this checklist as PR_NUMBER)

### Task 6: Generate the Changelog snippet using the generate_changelog tool

- [ ] **Verify working directory is clean**:
  ```bash
  git status
  ```
- [ ] **Run changelog generator**:
  ```bash
  go run ./cmd/generate_changelog --ai-summarize --incoming-pr <PR_NUMBER>
  ```
- [ ] **Push the newly generated changelog snippet**
  ```bash
  git push
  ```

### Task 7: Create Completion Summary

- [ ] **Write COMPLETION_SUMMARY.md**: Create `{{AUTORUN_FOLDER}}/COMPLETION_SUMMARY.md`:

```markdown
# Issue Resolution Summary

## Issue
- **Number**: #XXXX
- **Title**: [Title]
- **URL**: https://github.com/danielmiessler/fabric/issues/XXXX

## Resolution
- **Branch**: [branch name]
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

## Testing
- **Unit Tests**: [Pass count]
- **Coverage**: [If available]

## Generated Artifacts
- `ISSUE_CONTEXT.md` - Issue details
- `ISSUE_ANALYSIS.md` - Root cause analysis
- `IMPLEMENTATION_PLAN.md` - Phased plan
- `PHASE_N_IMPLEMENTATION.md` - Implementation records
- `TEST_RESULTS.md` - Test execution results
- `CHANGELOG.md` - Generated changelog
- `PR_DESCRIPTION.md` - Ready-to-use PR description
- `COMPLETION_SUMMARY.md` - This file

## Playbook Complete
The Fabric Issue Solver playbook has finished successfully.
```

### Task 8: Final Verification

- [ ] **Confirm all artifacts exist**:
  - [ ] `ISSUE_CONTEXT.md`
  - [ ] `ISSUE_ANALYSIS.md`
  - [ ] `IMPLEMENTATION_PLAN.md`
  - [ ] `PHASE_*_IMPLEMENTATION.md` (one per phase)
  - [ ] `TEST_RESULTS.md`
  - [ ] `CHANGELOG.md`
  - [ ] `PR_DESCRIPTION.md`
  - [ ] `COMPLETION_SUMMARY.md`

- [ ] **Verify branch is ready**:
  ```bash
  git status
  git log --oneline origin/main..HEAD
  ```

## Success Criteria

- All implementation complete
- Final tests pass
- Changelog generated
- PR description prepared
- Completion summary created
- Branch ready for push

## Status

Mark complete when all finalization artifacts are created.

---

## Playbook Complete

**Generated Files:**
- `PR_DESCRIPTION.md` - Copy this content when creating the PR
- `CHANGELOG.md` - Include in PR or commit
- `COMPLETION_SUMMARY.md` - Reference for what was done
