# Document 2: Evaluate PR-A

## Context

- **Playbook**: Best PR Comparison
- **Agent**: {{AGENT_NAME}}
- **Project**: {{AGENT_PATH}}
- **Date**: {{DATE}}
- **Working Folder**: {{AUTORUN_FOLDER}}

## Purpose

Perform a thorough evaluation of PR-A against the established criteria.

## Prerequisites

- `{{AUTORUN_FOLDER}}/EVALUATION_CRITERIA.md` exists from Document 1

## Tasks

### Task 1: Load Context

- [ ] **Read evaluation criteria**: Load `{{AUTORUN_FOLDER}}/EVALUATION_CRITERIA.md` to understand the rubric and PR-A details.

- [ ] **Re-fetch PR-A diff if needed**: Ensure you have the full diff available for analysis.

### Task 2: Evaluate Security (Weight: 3x)

- [ ] **Check input validation**: Are all user inputs validated? Look for:
  - Form inputs
  - API parameters
  - URL parameters
  - File uploads

- [ ] **Check for injection vulnerabilities**: Look for:
  - Raw SQL queries with string concatenation
  - Command execution with user input
  - Unescaped HTML output (XSS)

- [ ] **Check auth/authz**: Are protected resources properly guarded?

- [ ] **Check for exposed secrets**: Any hardcoded credentials, API keys, or tokens?

- [ ] **Assign Security score**: Excellent (5) / Good (4) / Fair (3) / Poor (2) / N/A (0)

### Task 3: Evaluate Correctness (Weight: 3x)

- [ ] **Check feature completeness**: Does the PR fully implement the intended feature?

- [ ] **Check edge cases**: Are boundary conditions handled?

- [ ] **Check error handling**: Are errors caught and handled appropriately?

- [ ] **Check null safety**: Are null/undefined values handled?

- [ ] **Check logic**: Is the business logic correct?

- [ ] **Assign Correctness score**: Excellent (5) / Good (4) / Fair (3) / Poor (2) / N/A (0)

### Task 4: Evaluate Performance (Weight: 2x)

- [ ] **Check algorithm efficiency**: Any O(n²) or worse where O(n) is possible?

- [ ] **Check for N+1 queries**: Database queries in loops?

- [ ] **Check memory usage**: Large objects held unnecessarily?

- [ ] **Check async handling**: Proper use of async/await, no blocking?

- [ ] **Assign Performance score**: Excellent (5) / Good (4) / Fair (3) / Poor (2) / N/A (0)

### Task 5: Evaluate Test Coverage (Weight: 2x)

- [ ] **Check for new tests**: Are tests added for new functionality?

- [ ] **Check test quality**: Are tests meaningful or just checking for "no error"?

- [ ] **Check edge case coverage**: Do tests cover boundary conditions?

- [ ] **Check mocking**: Is mocking appropriate (not over/under-mocked)?

- [ ] **Assign Test Coverage score**: Excellent (5) / Good (4) / Fair (3) / Poor (2) / N/A (0)

### Task 6: Evaluate Code Quality (Weight: 1x)

- [ ] **Check readability**: Is the code easy to understand?

- [ ] **Check patterns**: Does it follow existing codebase conventions?

- [ ] **Check DRY**: Any unnecessary duplication?

- [ ] **Check abstraction**: Right level of abstraction (not over/under-engineered)?

- [ ] **Assign Code Quality score**: Excellent (5) / Good (4) / Fair (3) / Poor (2) / N/A (0)

### Task 7: Evaluate Documentation (Weight: 1x)

- [ ] **Check code comments**: Are complex sections explained?

- [ ] **Check docs updates**: README or other docs updated if needed?

- [ ] **Check API docs**: New endpoints documented?

- [ ] **Assign Documentation score**: Excellent (5) / Good (4) / Fair (3) / Poor (2) / N/A (0)

### Task 8: Evaluate PR Size/Simplicity (Weight: 1x)

- [ ] **Check PR size**: How many lines added/removed? Files changed?

- [ ] **Check focus**: Is the PR focused or does it include unrelated changes?

- [ ] **Check complexity**: Does it introduce unnecessary complexity?

- [ ] **Assign PR Size score**: Excellent (5) / Good (4) / Fair (3) / Poor (2) / N/A (0)

### Task 9: Note Standout Features

- [ ] **Identify gems**: What does PR-A do particularly well? Any clever solutions, good patterns, or thoughtful touches that should be preserved regardless of outcome?

### Task 10: Create PR-A Evaluation Document

- [ ] **Write PR_A_EVALUATION.md**: Create `{{AUTORUN_FOLDER}}/PR_A_EVALUATION.md`:

```markdown
# PR-A Evaluation

**PR URL**: [URL]
**Evaluated**: {{DATE}}

## Scores Summary

| Category | Weight | Rating | Score | Weighted |
|----------|--------|--------|-------|----------|
| Security | 3x | [Rating] | [1-5] | [×3] |
| Correctness | 3x | [Rating] | [1-5] | [×3] |
| Performance | 2x | [Rating] | [1-5] | [×2] |
| Test Coverage | 2x | [Rating] | [1-5] | [×2] |
| Code Quality | 1x | [Rating] | [1-5] | [×1] |
| Documentation | 1x | [Rating] | [1-5] | [×1] |
| PR Size | 1x | [Rating] | [1-5] | [×1] |
| **Total** | | | | **[XX/65]** |

## Detailed Evaluation

### Security (Score: X/5)
[Detailed notes on security evaluation]

### Correctness (Score: X/5)
[Detailed notes on correctness evaluation]

### Performance (Score: X/5)
[Detailed notes on performance evaluation]

### Test Coverage (Score: X/5)
[Detailed notes on test coverage evaluation]

### Code Quality (Score: X/5)
[Detailed notes on code quality evaluation]

### Documentation (Score: X/5)
[Detailed notes on documentation evaluation]

### PR Size/Simplicity (Score: X/5)
[Detailed notes on PR size evaluation]

## Standout Features (Gems)
[What PR-A does particularly well - preserve these regardless of outcome]

## Concerns
[Any significant issues or red flags]
```

## Success Criteria

- ✅ All 7 categories evaluated with scores
- ✅ Detailed notes for each category
- ✅ Standout features identified
- ✅ Concerns documented
- ✅ PR_A_EVALUATION.md created

## Status

Mark complete when evaluation document is created.

---

**Next**: Document 3 will evaluate PR-B against the same criteria.
