# Finalize

## Context

- **Playbook:** Internationalization Cleanup
- **Agent:** {{AGENT_NAME}}
- **Project:** {{AGENT_PATH}}
- **Loop:** {{LOOP_NUMBER}}
- **Date:** {{DATE}}
- **Working Folder:** {{AUTORUN_FOLDER}}

## Purpose

Generate final summary, report unused keys, compile manual review items, and prepare PR description for the i18n cleanup work.

## Tasks

- [ ] **Read final state** of all working files:

```bash
cat {{AUTORUN_FOLDER}}/VIOLATIONS_PLAN.md
cat {{AUTORUN_FOLDER}}/UNUSED_KEYS.md
cat {{AUTORUN_FOLDER}}/MANUAL_REVIEW.md
cat {{AUTORUN_FOLDER}}/TEST_RESULTS.md
```

- [ ] **Count completed work**:

```bash
COMPLETE_COUNT=$(grep -c '| COMPLETE |' {{AUTORUN_FOLDER}}/VIOLATIONS_PLAN.md || echo 0)
SKIP_COUNT=$(grep -c '| SKIP |' {{AUTORUN_FOLDER}}/VIOLATIONS_PLAN.md || echo 0)
MANUAL_COUNT=$(grep -c '| MANUAL_REVIEW |' {{AUTORUN_FOLDER}}/VIOLATIONS_PLAN.md || echo 0)
TOTAL_FILES=$(grep -c '^|' {{AUTORUN_FOLDER}}/VIOLATIONS_PLAN.md | awk '{print $1-2}')  # Subtract header rows

echo "Files processed: $COMPLETE_COUNT"
echo "Files skipped: $SKIP_COUNT"
echo "Files needing manual review: $MANUAL_COUNT"
echo "Total files: $TOTAL_FILES"
```

- [ ] **Count manual review items**:

```bash
REVIEW_ITEMS=$(grep -c '^###' {{AUTORUN_FOLDER}}/MANUAL_REVIEW.md || echo 0)
echo "Manual review items flagged: $REVIEW_ITEMS"
```

- [ ] **Count unused keys**:

```bash
UNUSED_HIGH=$(grep -c '^|' {{AUTORUN_FOLDER}}/UNUSED_KEYS.md | awk '{print $1}' || echo 0)
echo "Unused i18n keys found: $UNUSED_HIGH"
```

- [ ] **Analyze test results**:

```bash
# Count how many loops had passing tests
PASS_COUNT=$(grep -c '✅ VERIFIED' {{AUTORUN_FOLDER}}/TEST_RESULTS.md || echo 0)
FAIL_COUNT=$(grep -c '❌ FAILED' {{AUTORUN_FOLDER}}/TEST_RESULTS.md || echo 0)

echo "Verification passes: $PASS_COUNT"
echo "Verification failures: $FAIL_COUNT"
```

- [ ] **Generate PR description**:

Create `{{AUTORUN_FOLDER}}/PR_DESCRIPTION.md`:

```markdown
# i18n Cleanup: Replace Hard-Coded Strings

## Summary

This PR systematically replaces hard-coded strings with proper internationalization (i18n) calls across **$COMPLETE_COUNT files**. All strings have been translated for **[number]** supported languages using AI-assisted translation with manual review where needed.

## Changes

### Files Modified

- **$COMPLETE_COUNT files** with i18n violations fixed
- **[number] translation files** updated with new keys
- **$SKIP_COUNT files** intentionally skipped (specify reasons)

### String Categories Fixed

- ✅ Error messages
- ✅ CLI output and help text
- ✅ Log messages
- ✅ UI labels and prompts

### Translations Added

Added **[number] new i18n keys** with translations for:
- English (en)
- Spanish (es)
- French (fr)
- [... list all supported languages ...]

### Verification

- ✅ All tests passing: $PASS_COUNT/$((PASS_COUNT + FAIL_COUNT)) verifications succeeded
- ✅ Build verification passed
- ⚠️ **$REVIEW_ITEMS items flagged for manual review** (see details below)

## Manual Review Required

The following items need human review before merging:

[Insert section from MANUAL_REVIEW.md, or state "None"]

## Unused Keys Report

Found **$UNUSED_HIGH unused i18n keys** that can be cleaned up:

[Insert high-confidence section from UNUSED_KEYS.md, or link to file]

## Testing

Each file was verified after modification:
1. Test suite executed: \`[test command]\`
2. Build verification: \`[build command]\`
3. All interpolations and placeholders validated

## Checklist

- [ ] All files with PENDING status processed
- [ ] Translation files updated for all supported languages
- [ ] Tests passing
- [ ] Build successful
- [ ] Manual review items documented
- [ ] Unused keys identified for future cleanup

## Related Issues

Closes: [issue number if applicable]

## Notes

[Any additional context about the changes, edge cases handled, or follow-up work needed]
```

- [ ] **Generate cleanup summary**:

Create `{{AUTORUN_FOLDER}}/CLEANUP_SUMMARY.md`:

```markdown
# i18n Cleanup Summary

**Playbook:** Internationalization Cleanup
**Date:** {{DATE}}
**Agent:** {{AGENT_NAME}}
**Project:** {{AGENT_PATH}}

## Overview

This playbook execution cleaned up hard-coded strings across the codebase by replacing them with proper i18n patterns.

## Statistics

| Metric | Count |
|--------|-------|
| Files analyzed | $TOTAL_FILES |
| Files fixed | $COMPLETE_COUNT |
| Files skipped | $SKIP_COUNT |
| Files needing manual review | $MANUAL_COUNT |
| Translation keys added | [count from implementation logs] |
| Languages supported | [count from I18N_SETUP.md] |
| Manual review items | $REVIEW_ITEMS |
| Unused keys found | $UNUSED_HIGH |
| Loop iterations | {{LOOP_NUMBER}} |

## Verification Results

| Metric | Count |
|--------|-------|
| Successful verifications | $PASS_COUNT |
| Failed verifications | $FAIL_COUNT |
| Overall success rate | [percentage]% |

## Files Modified

### Completed

[List of all files with COMPLETE status from VIOLATIONS_PLAN.md]

### Skipped

[List of all files with SKIP status and reasons]

### Manual Review Needed

[List of all files with MANUAL_REVIEW status]

## Translation Keys Added

See individual `FILE_N_FIXES.md` files for detailed mappings.

**Sample keys:**
[Show 5-10 example keys as a preview]

## Manual Review Items

See `MANUAL_REVIEW.md` for full details. Key items:

[Summarize the most important manual review items]

## Unused Keys

See `UNUSED_KEYS.md` for complete list. Consider removing:

[List high-confidence unused keys]

## Recommendations

1. **Before merging:**
   - Review all items in `MANUAL_REVIEW.md`
   - Verify translations are contextually appropriate
   - Consider native speaker review for critical user-facing strings

2. **After merging:**
   - Clean up unused keys identified in `UNUSED_KEYS.md`
   - Update contributing guidelines to emphasize i18n requirements
   - Add linter rules to catch future hard-coded strings

3. **Ongoing:**
   - Run this playbook periodically to catch new violations
   - Update translation files when adding new features
   - Consider automation to prevent hard-coded strings in CI

## Files Generated

- `I18N_CONTEXT.md` - Configuration
- `I18N_SETUP.md` - Detected i18n pattern
- `VIOLATIONS_PLAN.md` - File-by-file status
- `UNUSED_KEYS.md` - Dead key analysis
- `FILE_N_FIXES.md` - Per-file implementation logs ({{LOOP_NUMBER}} files)
- `MANUAL_REVIEW.md` - Items needing human review
- `TEST_RESULTS.md` - Verification outputs
- `PR_DESCRIPTION.md` - Ready-to-use PR text
- `CLEANUP_SUMMARY.md` - This file

## Next Steps

1. Review `MANUAL_REVIEW.md` and address flagged items
2. Review `PR_DESCRIPTION.md` and create pull request
3. Get translation review from native speakers if available
4. Merge PR
5. Clean up unused keys
6. Update project documentation/guidelines

## Success Criteria

✅ Hard-coded strings replaced with i18n calls
✅ Translations generated for all supported languages
✅ Tests passing
✅ Build successful
✅ Manual review items documented
⚠️ Unused keys identified for cleanup

**Overall Status:** [SUCCESS | SUCCESS WITH MANUAL REVIEW NEEDED | PARTIAL - SEE NOTES]
```

- [ ] **Validate all deliverables exist**:

```bash
echo "=== Checking Final Deliverables ==="
for file in I18N_CONTEXT.md I18N_SETUP.md VIOLATIONS_PLAN.md UNUSED_KEYS.md MANUAL_REVIEW.md TEST_RESULTS.md PR_DESCRIPTION.md CLEANUP_SUMMARY.md; do
  if [ -f "{{AUTORUN_FOLDER}}/$file" ]; then
    echo "✅ $file"
  else
    echo "❌ MISSING: $file"
  fi
done

# Check for implementation logs
FILE_LOG_COUNT=$(ls {{AUTORUN_FOLDER}}/FILE_*_FIXES.md 2>/dev/null | wc -l)
echo "✅ $FILE_LOG_COUNT implementation logs"
```

- [ ] **Unstage tracking files** - Ensure playbook working files aren't committed:

```bash
# CRITICAL: These tracking files are for playbook use only - DO NOT commit them
# They track our progress but are not part of the repository changes

echo "Ensuring tracking files are not staged for commit..."

# Unstage all playbook tracking files
git reset HEAD {{AUTORUN_FOLDER}}/I18N_CONTEXT.md 2>/dev/null || true
git reset HEAD {{AUTORUN_FOLDER}}/I18N_SETUP.md 2>/dev/null || true
git reset HEAD {{AUTORUN_FOLDER}}/VIOLATIONS_PLAN.md 2>/dev/null || true
git reset HEAD {{AUTORUN_FOLDER}}/UNUSED_KEYS.md 2>/dev/null || true
git reset HEAD {{AUTORUN_FOLDER}}/MANUAL_REVIEW.md 2>/dev/null || true
git reset HEAD {{AUTORUN_FOLDER}}/FILE_*_FIXES.md 2>/dev/null || true
git reset HEAD {{AUTORUN_FOLDER}}/TEST_RESULTS.md 2>/dev/null || true
git reset HEAD {{AUTORUN_FOLDER}}/test_output_*.log 2>/dev/null || true
git reset HEAD {{AUTORUN_FOLDER}}/build_output_*.log 2>/dev/null || true

echo "✅ Tracking files excluded from git staging"
echo ""
echo "NOTE: PR_DESCRIPTION.md and CLEANUP_SUMMARY.md can optionally be committed"
echo "      as documentation if desired, but are not required."
```

- [ ] **Final summary**:

```bash
echo ""
echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
echo "✅ INTERNATIONALIZATION CLEANUP COMPLETE"
echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
echo ""
echo "📊 Summary:"
echo "   • Files fixed: $COMPLETE_COUNT"
echo "   • Manual review items: $REVIEW_ITEMS"
echo "   • Unused keys found: $UNUSED_HIGH"
echo "   • Loop iterations: {{LOOP_NUMBER}}"
echo ""
echo "📄 Next Steps:"
echo "   1. Review: {{AUTORUN_FOLDER}}/MANUAL_REVIEW.md"
echo "   2. Create PR: {{AUTORUN_FOLDER}}/PR_DESCRIPTION.md"
echo "   3. Full summary: {{AUTORUN_FOLDER}}/CLEANUP_SUMMARY.md"
echo ""
echo "⚠️  IMPORTANT: Tracking files have been excluded from git staging"
echo "   Only commit source code changes and translation file updates"
echo ""
echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
```

## Notes

This document completes the playbook. All working files have been generated and the changes are ready for pull request creation.

**What to commit:**
- Source code changes (i18n function calls replacing hard-coded strings)
- Translation file updates (new keys added)
- Optionally: PR_DESCRIPTION.md and CLEANUP_SUMMARY.md as documentation

**What NOT to commit (tracking files only):**
- I18N_CONTEXT.md
- I18N_SETUP.md
- VIOLATIONS_PLAN.md
- UNUSED_KEYS.md
- MANUAL_REVIEW.md
- FILE_*_FIXES.md
- TEST_RESULTS.md
- test_output_*.log
- build_output_*.log

These tracking files are automatically unstaged to prevent accidental commits.
