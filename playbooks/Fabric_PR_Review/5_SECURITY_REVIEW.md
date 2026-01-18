# Document 5: Security Review

## Context

- **Playbook**: Fabric PR Review
- **Agent**: {{AGENT_NAME}}
- **Project**: {{AGENT_PATH}}
- **Date**: {{DATE}}
- **Working Folder**: {{AUTORUN_FOLDER}}

## Purpose

Perform a security-focused review of the code changes, checking for vulnerabilities specific to Fabric's architecture.

## Prerequisites

- `{{AUTORUN_FOLDER}}/REVIEW_SCOPE.md` exists from Document 1

## Tasks

### Task 1: Load Context

- [ ] **Read scope**: Load `{{AUTORUN_FOLDER}}/REVIEW_SCOPE.md` to identify high-risk files.

### Task 2: Check for Secrets and Credentials

- [ ] **Hardcoded secrets**: Search for:
  - API keys (OpenAI, Anthropic, Gemini, etc.)
  - Passwords or tokens
  - Private keys
  - Connection strings
  - OAuth credentials

- [ ] **Config file handling**: Verify:
  - Secrets loaded from `~/.config/fabric/.env`
  - Environment variables used, not hardcoded values
  - No secrets in code comments
  - `.gitignore` excludes sensitive files

### Task 3: Check OWASP Top 10

- [ ] **Injection vulnerabilities**:
  - Command injection (shell commands with user input)
  - SQL injection (for database plugins)
  - Template injection (pattern variable handling)
  - LDAP/XPath injection (if applicable)

- [ ] **Sensitive data exposure**:
  - API keys not logged
  - No PII in debug output
  - Errors don't expose internal paths
  - Responses don't leak system info

- [ ] **Broken access control**:
  - Server endpoints check authorization
  - No path traversal vulnerabilities
  - Pattern loading respects boundaries
  - Custom patterns isolated from built-in

- [ ] **Security misconfiguration**:
  - Debug mode disabled by default
  - No default credentials
  - Secure defaults for server mode
  - CORS properly configured (if applicable)

- [ ] **Insecure deserialization**:
  - JSON/YAML parsing is safe
  - No unsafe unmarshaling of user input
  - Type checking before deserialization

### Task 4: Fabric-Specific Security

- [ ] **API key handling**:
  - Provider API keys stored securely
  - Keys not exposed in URLs or logs
  - OAuth tokens properly managed
  - Key rotation considerations

- [ ] **Pattern security**:
  - Custom patterns can't override system paths
  - Pattern variables are sanitized
  - No code execution via patterns
  - Template extensions are sandboxed

- [ ] **Server mode security**:
  - `/api/` endpoints are protected
  - Rate limiting considered
  - Input validation on all endpoints
  - SSE streams don't leak data

- [ ] **Ollama compatibility mode**:
  - Proper request validation
  - No privilege escalation
  - Secure proxy behavior

### Task 5: Check Third-Party Dependencies

- [ ] **New dependencies**: If `go.mod` or `go.sum` changed:
  - List new dependencies added
  - Check for known vulnerabilities
  - Verify license compatibility
  - Assess dependency maintainability

- [ ] **Dependency updates**: If existing deps updated:
  - Check changelog for security fixes
  - Verify no breaking changes
  - Test compatibility

### Task 6: Review Authentication Code

If auth-related code changed:

- [ ] **OAuth implementation**:
  - Token storage is secure
  - Refresh flow is correct
  - No token exposure in logs
  - Proper session handling

- [ ] **API key validation**:
  - Timing-safe comparison
  - Proper error messages (no info leak)
  - Rate limiting for auth failures

### Task 7: Document Security Issues

- [ ] **Create SECURITY_ISSUES.md**: Write findings to `{{AUTORUN_FOLDER}}/SECURITY_ISSUES.md`:

```markdown
# Security Review Findings

## Critical Vulnerabilities
[Immediate security risks - must block merge]

## High Risk Issues
[Significant security concerns]

## Medium Risk Issues
[Security improvements needed]

## Low Risk Issues
[Minor security hardening suggestions]

## Secrets/Credentials Check
- [ ] No hardcoded API keys found
- [ ] No exposed credentials
- [ ] Proper use of environment variables

## OWASP Compliance
- [ ] No injection vulnerabilities
- [ ] No sensitive data exposure
- [ ] Proper access control
- [ ] Secure configuration

## Fabric-Specific Security
- [ ] API key handling secure
- [ ] Pattern system secure
- [ ] Server mode secure

## Dependencies
[New or updated dependencies with security implications]

## Positive Observations
[Good security practices noted]

## No Issues Found
[Areas reviewed with no concerns]
```

For each issue include:
- Vulnerability type
- File and line number
- Description and potential impact
- Remediation recommendation
- Severity: Critical / High / Medium / Low

## Success Criteria

- All changed files reviewed for security
- OWASP categories checked
- Fabric-specific security verified
- No hardcoded secrets found (or flagged)
- SECURITY_ISSUES.md created

## Status

Mark complete when security review document is created.

---

**Next**: Document 6 will run tests and generate changelog.
