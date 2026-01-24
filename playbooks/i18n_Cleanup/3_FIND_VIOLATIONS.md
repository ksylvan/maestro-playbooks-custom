# Find Violations

## Context

- **Playbook:** Internationalization Cleanup
- **Agent:** {{AGENT_NAME}}
- **Project:** {{AGENT_PATH}}
- **Loop:** {{LOOP_NUMBER}}
- **Date:** {{DATE}}
- **Working Folder:** {{AUTORUN_FOLDER}}

## Purpose

Scan the configured scope for hard-coded strings in user-facing messages, detect unused i18n keys, and create a prioritized plan for fixing violations.

## Tasks

- [ ] **Read previous analysis**:

```bash
cat {{AUTORUN_FOLDER}}/I18N_CONTEXT.md
cat {{AUTORUN_FOLDER}}/I18N_SETUP.md
```

- [ ] **Extract scan scope** from configuration:

```bash
# Read the configured scope from I18N_CONTEXT.md
SCAN_SCOPE=$(grep "Scan Scope:" {{AUTORUN_FOLDER}}/I18N_CONTEXT.md | cut -d: -f2 | xargs)
echo "Scanning scope: $SCAN_SCOPE"
```

- [ ] **Scan for hard-coded strings** - Search for common patterns of user-facing strings:

Look for (adapt based on detected language in I18N_SETUP.md):

**Error messages:**
- Go: `fmt.Errorf("...")`, `errors.New("...")`
- JavaScript: `throw new Error("...")`, `console.error("...")`
- Python: `raise Exception("...")`, `ValueError("...")`
- Ruby: `raise StandardError.new("...")`

**CLI output:**
- Go: `fmt.Println("...")`, `fmt.Printf("...")`
- JavaScript: `console.log("...")`
- Python: `print("...")`
- Ruby: `puts "..."`

**Log messages:**
- All languages: logger calls with string literals

**UI labels:**
- React: `<button>Hard coded text</button>`, `placeholder="..."`
- Vue: `<div>Hard coded text</div>`
- HTML templates with string literals

Use grep/ripgrep patterns appropriate to the codebase language and structure.

- [ ] **Analyze each file** with violations - For each file found, count violations and categorize:

Group files by:
1. **Violation count** (high to low priority)
2. **File type** (plugins, core, tests, docs)
3. **Complexity** (simple strings vs. complex interpolations)

- [ ] **Detect unused i18n keys** - Find translation keys defined but never referenced:

Strategy:
1. Extract all keys from translation files (based on I18N_SETUP.md structure)
2. Search codebase for usage of each key
3. Keys with zero references are unused

Create `{{AUTORUN_FOLDER}}/UNUSED_KEYS.md`:

```markdown
# Unused i18n Keys

Keys defined in translation files but never referenced in the codebase.

## High Confidence (No References Found)

| Key | File | Languages |
|-----|------|-----------|
| [key.name] | [translation file] | en, es, fr |

## Medium Confidence (May Be Dynamic)

| Key | File | Reason |
|-----|------|--------|
| [key.name] | [file] | Could be dynamically constructed |

## Recommendation

Consider removing high-confidence unused keys to reduce maintenance burden.
Review medium-confidence keys to determine if they're truly unused or accessed dynamically.

**Total Unused Keys:** [count]
```

- [ ] **Create violations plan** - Prioritized list of files to fix:

Create `{{AUTORUN_FOLDER}}/VIOLATIONS_PLAN.md`:

```markdown
# i18n Violations Plan

## Summary

- **Files with violations:** [count]
- **Total hard-coded strings:** [estimated count]
- **Scan scope:** [from I18N_CONTEXT.md]
- **Date:** {{DATE}}

## Files to Fix (Prioritized)

| # | File | Violations | Type | Status |
|---|------|------------|------|--------|
| 1 | [path/to/file1.ext] | 15 | Error messages + CLI output | PENDING |
| 2 | [path/to/file2.ext] | 12 | Error messages | PENDING |
| 3 | [path/to/file3.ext] | 8 | UI labels | PENDING |
| 4 | [path/to/file4.ext] | 5 | Log messages | PENDING |
| ... | ... | ... | ... | PENDING |

## Status Legend

- **PENDING**: Ready for automated fixing
- **IN_PROGRESS**: Currently being processed
- **COMPLETE**: All violations fixed and verified
- **MANUAL_REVIEW**: Requires human review before automation
- **SKIP**: Intentionally skipped (generated code, vendored, etc.)

## Exclusions

Files excluded from scan:
- `vendor/`, `node_modules/`, `.git/` (dependency directories)
- `*_test.go`, `*.test.js` (test files - may be lower priority)
- Generated files (marked with code generation headers)

## Notes

[Any important observations about the violations found]
```

- [ ] **Initialize manual review file**:

Create `{{AUTORUN_FOLDER}}/MANUAL_REVIEW.md`:

```markdown
# Manual Review Items

Strings flagged during automated fixing that need human review.

## Items

(Will be populated during implementation phase)

---

## Review Criteria

Strings are flagged when:
- Complex interpolation that may not translate directly
- Context-dependent meaning (same English phrase, different contexts)
- Technical terms that may not need translation
- User-facing vs. developer-facing ambiguity
```

- [ ] **Validate plan completeness**:

```bash
echo "✅ Violations scan complete"
echo "✅ Found files with hard-coded strings"
echo "✅ VIOLATIONS_PLAN.md created with prioritized file list"
echo "✅ UNUSED_KEYS.md created with dead key analysis"
echo "✅ MANUAL_REVIEW.md initialized"
echo "✅ Ready to proceed to Document 4: Implementation Loop"
```

## Notes

This document completes the analysis phase. Documents 4-5 will loop through the violations plan, fixing one file per iteration until all files are marked COMPLETE or SKIP.
