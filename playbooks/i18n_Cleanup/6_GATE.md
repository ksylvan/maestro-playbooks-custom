# i18n Cleanup Loop Gate

## Context

- **Playbook:** Internationalization Cleanup
- **Agent:** {{AGENT_NAME}}
- **Project:** {{AGENT_PATH}}
- **Loop:** {{LOOP_NUMBER}}
- **Date:** {{DATE}}
- **Working Folder:** {{AUTORUN_FOLDER}}

## Purpose

This is the **loop gate** and **finalization** document. It has `resetOnCompletion: false` and is the only non-reset document in the implementation loop. Maestro continues looping as long as this document has unchecked tasks. When all tasks are checked off, the loop exits.

## Gate Decision

Read `{{AUTORUN_FOLDER}}/VIOLATIONS_PLAN.md` and check file statuses:

- If any files have status `PENDING` → **leave the task below unchecked** (loop continues)
- If no files have status `PENDING` (all are COMPLETE, SKIP, MANUAL_REVIEW, or WON'T DO) → **check the task below** and proceed to finalization

- [ ] **All i18n violations resolved** — No PENDING files remain in `VIOLATIONS_PLAN.md`. All files have been processed, skipped, or flagged for manual review. Display a summary: (1) COMPLETE count, (2) PENDING count, (3) MANUAL_REVIEW count, (4) SKIP count, (5) decision.

## Finalization

The following tasks generate final deliverables for the i18n cleanup effort.

- [ ] **Read final state** of all working files. Read these files directly (do NOT use grep/awk/cut to parse) and extract key metrics:
  - `VIOLATIONS_PLAN.md` — count files by status: COMPLETE, SKIP, MANUAL_REVIEW, PENDING
  - `MANUAL_REVIEW.md` — count flagged items
  - `UNUSED_KEYS.md` — count unused keys
  - `TEST_RESULTS.md` — count pass/fail results

- [ ] **Generate PR description** — Create `{{AUTORUN_FOLDER}}/PR_DESCRIPTION.md`:

  Write a PR description summarizing the i18n cleanup work. Include:
  - A summary paragraph with file counts and scope
  - List of modified files with their status
  - Languages translated
  - Verification results (pass/fail counts)
  - Manual review items that need attention before merging
  - Any unused keys identified for future cleanup

  Keep it concise — a well-written PR description is 30-50 lines, not 75. Base all content on the actual working files you just read, not template placeholders.

- [ ] **Generate cleanup summary** — Create `{{AUTORUN_FOLDER}}/CLEANUP_SUMMARY.md`:

  Write a summary of the entire playbook run. Include:
  - Statistics table: files analyzed, fixed, skipped, keys added, languages, loop count
  - Verification results (pass/fail with success rate)
  - List of files modified, grouped by status (complete, skipped, manual review)
  - Manual review items summary
  - Unused keys summary
  - Recommendations for before merging, after merging, and ongoing maintenance

  Keep it under 80 lines. Base all content on the actual working files.

- [ ] **Validate all deliverables exist** — Confirm the following files are present in `{{AUTORUN_FOLDER}}`:
  - `I18N_CONTEXT.md`
  - `I18N_SETUP.md`
  - `VIOLATIONS_PLAN.md`
  - `UNUSED_KEYS.md`
  - `MANUAL_REVIEW.md`
  - `TEST_RESULTS.md`
  - `PR_DESCRIPTION.md`
  - `CLEANUP_SUMMARY.md`
  - `FILE_*_FIXES.md` (one per loop iteration)

  Report any missing files.

- [ ] **Unstage tracking files** — Ensure playbook working files aren't committed:

```bash
# CRITICAL: These tracking files are for playbook use only - DO NOT commit them
echo "Ensuring tracking files are not staged for commit..."

git reset HEAD {{AUTORUN_FOLDER}}/I18N_CONTEXT.md 2>/dev/null || true
git reset HEAD {{AUTORUN_FOLDER}}/I18N_SETUP.md 2>/dev/null || true
git reset HEAD {{AUTORUN_FOLDER}}/VIOLATIONS_PLAN.md 2>/dev/null || true
git reset HEAD {{AUTORUN_FOLDER}}/UNUSED_KEYS.md 2>/dev/null || true
git reset HEAD {{AUTORUN_FOLDER}}/MANUAL_REVIEW.md 2>/dev/null || true
git reset HEAD {{AUTORUN_FOLDER}}/FILE_*_FIXES.md 2>/dev/null || true
git reset HEAD {{AUTORUN_FOLDER}}/TEST_RESULTS.md 2>/dev/null || true
git reset HEAD {{AUTORUN_FOLDER}}/test_output_*.log 2>/dev/null || true
git reset HEAD {{AUTORUN_FOLDER}}/build_output_*.log 2>/dev/null || true

echo "Tracking files excluded from git staging"
echo ""
echo "NOTE: PR_DESCRIPTION.md and CLEANUP_SUMMARY.md can optionally be committed"
echo "      as documentation if desired, but are not required."
```

- [ ] **Final summary** — Report the completion status:
  - Files fixed, skipped, and needing manual review
  - Number of manual review items
  - Number of unused keys found
  - Loop iteration count ({{LOOP_NUMBER}})
  - Remind the user to review `MANUAL_REVIEW.md`, use `PR_DESCRIPTION.md` for PR creation, and check `CLEANUP_SUMMARY.md` for the full summary
  - Note that tracking files have been excluded from git staging

## Notes

**What to commit:**
- Source code changes (i18n function calls replacing hard-coded strings)
- Translation file updates (new keys added)
- Optionally: PR_DESCRIPTION.md and CLEANUP_SUMMARY.md as documentation

**What NOT to commit (tracking files only):**
- I18N_CONTEXT.md, I18N_SETUP.md, VIOLATIONS_PLAN.md
- UNUSED_KEYS.md, MANUAL_REVIEW.md, FILE_*_FIXES.md
- TEST_RESULTS.md, test_output_*.log, build_output_*.log
