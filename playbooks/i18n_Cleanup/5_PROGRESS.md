# i18n Cleanup Progress Gate

## Context

- **Playbook:** Internationalization Cleanup
- **Agent:** {{AGENT_NAME}}
- **Project:** {{AGENT_PATH}}
- **Loop:** {{LOOP_NUMBER}}
- **Date:** {{DATE}}
- **Working Folder:** {{AUTORUN_FOLDER}}

## Purpose

This document is the **progress gate** for the i18n cleanup pipeline. It verifies the most recent fix, then checks whether more work remains. **This is the only document with Reset ON** — it controls loop continuation by resetting tasks in documents 4-5 when more files need processing.

## Instructions

1. Read the latest `FILE_*_FIXES.md` from `{{AUTORUN_FOLDER}}`
2. Run tests and build verification
3. Update test results
4. Check for remaining PENDING files in `VIOLATIONS_PLAN.md`
5. If work remains → Reset documents 4-5 to continue
6. If no work remains → Generate final deliverables and exit

## Task 1: Load Latest Implementation

- [ ] **Read latest fix**: Find the most recent `FILE_*_FIXES.md` in `{{AUTORUN_FOLDER}}` and review the changes made.

```bash
LATEST_FIX=$(ls -t {{AUTORUN_FOLDER}}/FILE_*_FIXES.md 2>/dev/null | head -1)

if [ -n "$LATEST_FIX" ]; then
  echo "=== Latest Implementation ==="
  cat "$LATEST_FIX"
else
  echo "No implementation files found yet - analysis phase complete"
fi
```

## Task 2: Run Tests

- [ ] **Determine test command** from I18N_SETUP.md. Read `{{AUTORUN_FOLDER}}/I18N_SETUP.md` and extract the **Command** from the Test Command section. Run the test command and capture output to `{{AUTORUN_FOLDER}}/test_output_loop_{{LOOP_NUMBER}}.log`. Report PASS or FAIL with exit code.

## Task 3: Verify Build

- [ ] **Run build verification** (if applicable): Read the **Build Command** from `{{AUTORUN_FOLDER}}/I18N_SETUP.md`. Run that command and capture the output to `{{AUTORUN_FOLDER}}/build_output_loop_{{LOOP_NUMBER}}.log`. If no build command was configured, skip build verification.

## Task 4: Update Test Results

- [ ] **Update TEST_RESULTS.md** — Append to (or create) `{{AUTORUN_FOLDER}}/TEST_RESULTS.md`:

```markdown
## Loop {{LOOP_NUMBER}} - {{DATE}}

**File Processed:** [from latest FILE_N_FIXES.md]

### Test Results
- **Command:** [test command used]
- **Exit Code:** [exit code]
- **Result:** [PASS | FAIL]
- **Output:** See `test_output_loop_{{LOOP_NUMBER}}.log`

### Build Verification
- **Exit Code:** [exit code]
- **Result:** [PASS | FAIL]
- **Output:** See `build_output_loop_{{LOOP_NUMBER}}.log`

### Overall Status: [VERIFIED | FAILED | NEEDS REVIEW]

---
```

## Task 5: Handle Failures

- [ ] **If verification failed**: If either tests or build failed:
  1. Review the failure logs
  2. Determine if it's related to i18n changes or pre-existing
  3. Update VIOLATIONS_PLAN.md to mark the file as `MANUAL_REVIEW` if needed
  4. Add details to MANUAL_REVIEW.md explaining the failure

## Task 6: Progress Gate Check

- [ ] **Check for remaining work**: Read `{{AUTORUN_FOLDER}}/VIOLATIONS_PLAN.md` and count files by status. The loop should CONTINUE (reset docs 4-5) if there are items with status `PENDING`. The loop should EXIT (do NOT reset) when no `PENDING` items remain (all are COMPLETE, SKIP, MANUAL_REVIEW, or WON'T DO).

  Display a summary: (1) COMPLETE count, (2) PENDING count, (3) MANUAL_REVIEW count, (4) SKIP count, (5) decision.

## Reset Tasks (Only if work remains)

If the progress gate check above determines we need to continue (PENDING files remain), reset all tasks in the following documents:

- [ ] **Reset 4_IMPLEMENT.md**: Uncheck all tasks in `{{AUTORUN_FOLDER}}/4_IMPLEMENT.md`
- [ ] **Reset 5_PROGRESS.md**: Uncheck all tasks in `{{AUTORUN_FOLDER}}/5_PROGRESS.md`

**IMPORTANT**: Only reset documents 4-5 if there are PENDING files remaining. If all files are COMPLETE, SKIP, MANUAL_REVIEW, or WON'T DO, leave these reset tasks unchecked to allow the pipeline to exit to the finalization tasks below.

## Decision Logic

```
IF VIOLATIONS_PLAN.md doesn't exist:
    → Do NOT reset anything (PIPELINE JUST STARTED - LET IT RUN)

ELSE IF items with status `PENDING` exist:
    → Reset documents 4-5 (CONTINUE TO NEXT FILE)

ELSE:
    → Do NOT reset anything (ALL FILES PROCESSED - EXIT AND FINALIZE)
```

## How This Works

This document controls loop continuation through resets:
- **Reset tasks checked** → Documents 4-5 get reset → Loop continues
- **Reset tasks unchecked** → Nothing gets reset → Pipeline exits after finalization below

## Finalization (Only when exiting — all PENDING = 0)

The following tasks run ONLY when the loop is exiting (no reset tasks checked above). They generate final deliverables for the i18n cleanup effort.

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

## Manual Override

**To force exit early:**
- Leave all reset tasks unchecked regardless of remaining PENDING items
- The finalization tasks below will still run

**To continue past natural exit:**
- Check the reset tasks even when no PENDING files remain
- Useful if you want to re-scan for new violations

## Notes

- Test failures may indicate pre-existing issues unrelated to i18n changes — investigate before blaming the fix.
- Quality matters more than 100% coverage — document skip/manual-review decisions for future reference.
- Reset tasks checked → Documents 4-5 reset → Loop continues.
- Reset tasks unchecked → Nothing resets → Finalization runs → Pipeline exits.

**What to commit:**
- Source code changes (i18n function calls replacing hard-coded strings)
- Translation file updates (new keys added)
- Optionally: PR_DESCRIPTION.md and CLEANUP_SUMMARY.md as documentation

**What NOT to commit (tracking files only):**
- I18N_CONTEXT.md, I18N_SETUP.md, VIOLATIONS_PLAN.md
- UNUSED_KEYS.md, MANUAL_REVIEW.md, FILE_*_FIXES.md
- TEST_RESULTS.md, test_output_*.log, build_output_*.log
