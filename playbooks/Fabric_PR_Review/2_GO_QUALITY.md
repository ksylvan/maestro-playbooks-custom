# Document 2: Go Code Quality Review

## Context

- **Playbook**: Fabric PR Review
- **Agent**: {{AGENT_NAME}}
- **Project**: {{AGENT_PATH}}
- **Date**: {{DATE}}
- **Working Folder**: {{AUTORUN_FOLDER}}

## Purpose

Perform a Go-specific code review focusing on Fabric's coding conventions, Go idioms, and best practices.

## Prerequisites

- `{{AUTORUN_FOLDER}}/REVIEW_SCOPE.md` exists from Document 1

## Tasks

### Task 1: Load Context

- [ ] **Read scope**: Load `{{AUTORUN_FOLDER}}/REVIEW_SCOPE.md` to identify Go files to review.

### Task 2: Check Go Idioms

- [ ] **Error handling**: Verify Fabric's error patterns:
  - Errors are returned, not panicked (no `panic()` in library code)
  - Use `pkg/errors` for wrapping: `errors.Wrap(err, "context")`
  - Error messages are lowercase, no punctuation
  - Errors don't expose sensitive information

- [ ] **Context usage**: Check `context.Context` patterns:
  - Context is the first parameter where applicable
  - Context is propagated through call chains
  - Cancellation is handled for long operations
  - Timeouts are set for external calls

- [ ] **Interface compliance**: Verify interfaces:
  - Functions accept interfaces, return concrete types
  - Interfaces are defined where they're used
  - No empty interfaces (`interface{}`) without good reason

### Task 3: Review Code Organization

- [ ] **Package structure**: Check:
  - `internal/` packages are truly internal
  - No circular dependencies
  - Clear package boundaries
  - Appropriate file sizes

- [ ] **Naming conventions**: Verify:
  - CamelCase for exported identifiers
  - camelCase for unexported identifiers
  - Meaningful, descriptive names
  - Acronyms are all caps (HTTP, API, ID)

- [ ] **Documentation**: Check:
  - Exported functions have doc comments
  - Package-level documentation exists
  - Complex logic is explained
  - No stale comments

### Task 4: Review Concurrency

- [ ] **Goroutine safety**: Look for:
  - Race conditions on shared state
  - Proper channel usage (closing, direction)
  - Context-aware goroutines
  - No goroutine leaks

- [ ] **Streaming**: For streaming responses:
  - Channels are properly buffered
  - Errors are communicated correctly
  - Cleanup happens on cancellation

### Task 5: Review API Changes

- [ ] **Breaking changes**: If public APIs change:
  - Are changes backward compatible?
  - Are deprecation notices added?
  - Is the CHANGELOG updated?

- [ ] **Function signatures**: Verify:
  - Context is first parameter
  - Options pattern for many parameters
  - Error is last return value

### Task 6: Check Fabric-Specific Patterns

- [ ] **Plugin patterns**: For AI providers:
  - Implement `VendorPlugin` interface
  - Handle streaming via callbacks
  - Support model listing
  - Handle context cancellation

- [ ] **Configuration patterns**: For config changes:
  - Environment variables via `godotenv`
  - Flags via `go-flags`
  - YAML config support

- [ ] **Logging patterns**: For logging:
  - Use standard `log` package
  - Debug levels via `--debug` flag
  - No sensitive data in logs

### Task 7: Run Static Analysis

- [ ] **Check for modernization**: Run:
  ```bash
  go run golang.org/x/tools/go/analysis/passes/modernize/cmd/modernize@latest ./...
  ```
  Note any suggestions.

- [ ] **Check formatting**: Run:
  ```bash
  gofmt -l .
  ```
  Flag any unformatted files.

- [ ] **Check vet**: Run:
  ```bash
  go vet ./...
  ```
  Note any issues.

### Task 8: Document Go Issues

- [ ] **Create GO_ISSUES.md**: Write findings to `{{AUTORUN_FOLDER}}/GO_ISSUES.md`:

```markdown
# Go Code Quality Issues

## Critical Issues
[Must fix - compiler errors, data races, panics]

## Major Issues
[Should fix - error handling, context misuse, interface violations]

## Minor Issues
[Nice to fix - naming, documentation, style]

## Suggestions
[Optional improvements, modernization opportunities]

## Static Analysis Results

### Modernize
[Results from modernize tool]

### Gofmt
[Unformatted files if any]

### Go Vet
[Vet issues if any]

## Positive Observations
[Good Go practices observed]
```

For each issue include:
- File and line number
- Issue description
- Suggested fix
- Severity: Critical / Major / Minor / Suggestion

## Success Criteria

- All Go files reviewed for idioms
- Error handling verified
- Context usage checked
- Static analysis completed
- GO_ISSUES.md created

## Status

Mark complete when Go review document is created.

---

**Next**: Document 3 will validate Fabric pattern system changes.
