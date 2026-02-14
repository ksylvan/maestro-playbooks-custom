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

- [ ] **Read and summarize final state** — Read these files and extract key metrics:
  - `VIOLATIONS_PLAN.md` — count files by status: COMPLETE, SKIP, MANUAL_REVIEW, PENDING
  - `MANUAL_REVIEW.md` — count flagged items
  - `UNUSED_KEYS.md` — count unused keys
  - `TEST_RESULTS.md` — count pass/fail results

  Do NOT use `grep`/`awk`/`cut` to parse these files — read them directly as an AI agent and extract the information.

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

- [ ] **Final summary** — Report the completion status:
  - Files fixed, skipped, and needing manual review
  - Number of manual review items
  - Number of unused keys found
  - Loop iteration count ({{LOOP_NUMBER}})
  - Remind the user to review `MANUAL_REVIEW.md`, use `PR_DESCRIPTION.md` for PR creation, and check `CLEANUP_SUMMARY.md` for the full summary
  - Note that tracking files have been excluded from git staging

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
