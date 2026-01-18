# Fabric PR Review Playbook

A Maestro Auto Run playbook for comprehensive pull request reviews of the [danielmiessler/fabric](https://github.com/danielmiessler/fabric) repository.

## What This Playbook Does

Performs a thorough, multi-pass code review specifically designed for Fabric PRs, examining Go code quality, Fabric's pattern system, plugin architecture, security, and test coverage.

### Review Areas

1. **Go Code Quality** - Error handling, context usage, interface compliance, Go idioms
2. **Fabric Patterns** - Pattern structure validation, variable syntax, Markdown formatting
3. **Plugin Architecture** - VendorPlugin interface compliance, provider registration
4. **Security** - OWASP vulnerabilities, API key handling, injection risks
5. **Test Coverage** - Go test execution, table-driven tests, coverage gaps
6. **Changelog** - Automated changelog generation for the PR

## Playbook Structure

This playbook follows a **linear 7-document pattern** (no looping):

```
1_ANALYZE_PR.md        - Understand PR scope, changed files, categorization
2_GO_QUALITY.md        - Go-specific code review and best practices
3_FABRIC_PATTERNS.md   - Pattern system validation
4_FABRIC_PLUGINS.md    - Plugin architecture compliance
5_SECURITY_REVIEW.md   - Security-focused analysis
6_RUN_TESTS.md         - Execute tests and generate changelog
7_SUMMARIZE.md         - Compile findings and create PR comment
```

## Prerequisites

- Maestro installed and configured
- Go 1.24+ installed
- Git repository with the PR branch checked out
- Agent with access to the Fabric codebase
- GitHub CLI (`gh`) installed for PR fetching

## Setup in Maestro

1. Place this folder in your Maestro Auto Run directory
2. Clone the Fabric repo and check out the PR branch
3. Update Document 1's PR URL to point to your pull request
4. Configure Auto Run settings:
   - **Loop Mode**: OFF (sequential execution)
   - **Max Loops**: N/A (no looping)
5. Run the playbook

## Execution Flow

1. **Document 1** - Analyzes PR diff, identifies changed files, categorizes by type
2. **Document 2** - Reviews Go code for idioms, error handling, context patterns
3. **Document 3** - Validates new/modified patterns follow Fabric conventions
4. **Document 4** - Checks plugin implementations for interface compliance
5. **Document 5** - Scans for security vulnerabilities and risks
6. **Document 6** - Runs `go test ./...` and generates changelog
7. **Document 7** - Summarizes all findings into actionable PR comment

## Working Files Generated

| File | Purpose |
|------|---------|
| `REVIEW_SCOPE.md` | PR context and changed file categorization |
| `GO_ISSUES.md` | Go code quality and idiom violations |
| `PATTERN_ISSUES.md` | Pattern system validation findings |
| `PLUGIN_ISSUES.md` | Plugin architecture compliance issues |
| `SECURITY_ISSUES.md` | Security vulnerabilities found |
| `TEST_RESULTS.md` | Test execution results and coverage |
| `CHANGELOG.md` | Auto-generated changelog for the PR |
| `REVIEW_SUMMARY.md` | Final consolidated review |
| `PR_COMMENT.md` | Ready-to-post GitHub PR comment |

## Customization

Edit the PR number in `1_ANALYZE_PR.md` before running:

```markdown
**Pull Request**: https://github.com/danielmiessler/fabric/pull/XXXX
```

## Fabric-Specific Checks

### Pattern Validation
- `system.md` present in pattern directories
- Variable syntax uses `{{.variable}}` format
- Markdown structure is proper
- No hardcoded API keys or secrets

### Plugin Architecture
- New providers implement `VendorPlugin` interface
- OpenAI-compatible vendors extend `openai_compatible`
- Proper model registration in plugin registry
- Streaming support implemented where applicable

### Go Code Quality
- Error wrapping with `pkg/errors`
- Context propagation for cancellation
- Table-driven tests with `t.Run()`
- No panics in library code

## Exit Conditions

Playbook completes when all 7 documents report `COMPLETED` status.

## Support

For issues, refer to:
- Maestro: https://github.com/pedramamini/Maestro
- Fabric: https://github.com/danielmiessler/fabric
