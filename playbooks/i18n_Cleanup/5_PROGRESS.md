# i18n Cleanup Progress Gate

## Context

- **Playbook:** Internationalization Cleanup
- **Agent:** {{AGENT_NAME}}
- **Project:** {{AGENT_PATH}}
- **Loop:** {{LOOP_NUMBER}}
- **Date:** {{DATE}}
- **Working Folder:** {{AUTORUN_FOLDER}}

## Purpose

This document verifies the most recent fix and checks whether more work remains. It has `resetOnCompletion: true` — Maestro automatically resets all tasks each loop iteration. The loop continuation decision is handled by `6_GATE.md`.

## Instructions

1. Read the latest `FILE_*_FIXES.md` from `{{AUTORUN_FOLDER}}`
2. Run tests and build verification
3. Update test results
4. Handle any failures

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

## Notes

- Test failures may indicate pre-existing issues unrelated to i18n changes — investigate before blaming the fix.
- Quality matters more than 100% coverage — document skip/manual-review decisions for future reference.
- Documents 4 and 5 have `resetOnCompletion: true` — Maestro resets them automatically each loop.
- Loop continuation and finalization are handled by `6_GATE.md` (non-reset).
