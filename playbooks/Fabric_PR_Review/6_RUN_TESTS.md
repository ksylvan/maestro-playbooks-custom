# Document 6: Run Tests & Generate Changelog

## Context

- **Playbook**: Fabric PR Review
- **Agent**: {{AGENT_NAME}}
- **Project**: {{AGENT_PATH}}
- **Date**: {{DATE}}
- **Working Folder**: {{AUTORUN_FOLDER}}

## Purpose

Execute the Fabric test suite and generate the required changelog for the PR.

## Prerequisites

- `{{AUTORUN_FOLDER}}/REVIEW_SCOPE.md` exists from Document 1
- Go 1.24+ installed
- Working directory is clean (`git status` shows no uncommitted changes)

## Tasks

### Task 1: Verify Environment

- [ ] **Check Go version**:
  ```bash
  go version
  ```
  Verify Go 1.24+ is installed.

- [ ] **Check working directory**:
  ```bash
  git status
  ```
  Working directory must be clean for changelog generation.

### Task 2: Build the Project

- [ ] **Build main binary**:
  ```bash
  go build -o fabric ./cmd/fabric
  ```
  Note any build errors.

- [ ] **Build helper binaries** (if changed):
  ```bash
  go build -o code2context ./cmd/code2context
  go build -o to_pdf ./cmd/to_pdf
  go build -o generate_changelog ./cmd/generate_changelog
  ```

### Task 3: Run Test Suite

- [ ] **Run all tests**:
  ```bash
  go test ./...
  ```
  Capture output including any failures.

- [ ] **Run with verbose output** (if failures):
  ```bash
  go test -v ./...
  ```
  Get detailed failure information.

- [ ] **Run specific package tests** (if scope is limited):
  ```bash
  go test -v ./internal/cli
  go test -v ./internal/core
  go test -v ./internal/plugins/ai/...
  ```
  Focus on changed packages.

### Task 4: Check Test Coverage

- [ ] **Run with coverage**:
  ```bash
  go test -cover ./...
  ```
  Note coverage percentages for changed packages.

- [ ] **Generate coverage report** (optional):
  ```bash
  go test -coverprofile=coverage.out ./...
  go tool cover -func=coverage.out
  ```

### Task 5: Run Nix Checks (if available)

- [ ] **Check Nix flake** (if Nix installed):
  ```bash
  nix flake check
  ```
  This runs formatting and other checks.

### Task 6: Generate Changelog

- [ ] **Extract PR number**: From `{{AUTORUN_FOLDER}}/REVIEW_SCOPE.md`, get the PR number.

- [ ] **Run changelog generator**:
  ```bash
  go run ./cmd/generate_changelog --ai-summarize --incoming-pr <PR_NUMBER>
  ```
  Capture the generated changelog.

- [ ] **Save changelog**: Copy output to `{{AUTORUN_FOLDER}}/CHANGELOG.md`

### Task 7: Identify Test Gaps

- [ ] **Check new code coverage**: For each new function/file:
  - Is there a corresponding `*_test.go` file?
  - Are test cases comprehensive?
  - Are table-driven tests used?

- [ ] **Review test quality**: Check that tests:
  - Use `t.Run()` for subtests
  - Have meaningful test names
  - Cover edge cases
  - Don't test implementation details

### Task 8: Document Test Results

- [ ] **Create TEST_RESULTS.md**: Write findings to `{{AUTORUN_FOLDER}}/TEST_RESULTS.md`:

```markdown
# Test Execution Results

## Build Status
- [ ] Main binary builds: [PASS/FAIL]
- [ ] Helper binaries build: [PASS/FAIL]

## Test Execution

### Full Test Suite
```
[Output from go test ./...]
```

### Test Summary
- Total tests: X
- Passed: X
- Failed: X
- Skipped: X

### Failed Tests
[List any failed tests with details]

### Coverage Report
| Package | Coverage |
|---------|----------|
| internal/cli | XX% |
| internal/core | XX% |
| [etc.] | XX% |

## Nix Flake Check
[Output if applicable, or "Nix not available"]

## Test Gaps Identified
[New code without test coverage]

## Test Quality Observations
[Comments on test structure and quality]
```

- [ ] **Save changelog**: Also save to `{{AUTORUN_FOLDER}}/CHANGELOG.md`:

```markdown
# Generated Changelog

## PR #XXXX: [Title]

[Auto-generated changelog content]
```

## Success Criteria

- Project builds successfully
- All tests pass (or failures documented)
- Coverage report generated
- Changelog generated successfully
- TEST_RESULTS.md created
- CHANGELOG.md created

## Status

Mark complete when test and changelog documents are created.

---

**Next**: Document 7 will compile all findings into a summary and PR comment.
