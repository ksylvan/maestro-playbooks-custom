# Document 5: Verify Progress (Loop Gate)

## Context

- **Playbook:** Internationalization Cleanup
- **Agent:** {{AGENT_NAME}}
- **Project:** {{AGENT_PATH}}
- **Loop:** {{LOOP_NUMBER}}
- **Date:** {{DATE}}
- **Working Folder:** {{AUTORUN_FOLDER}}

## Purpose

Verify the most recent i18n fix with tests and build checks, then determine whether to continue looping or exit to finalization.

**This is the LOOP GATE document** — it controls whether the playbook continues to the next file or proceeds to Document 6 (finalization). This document has `resetOnCompletion: true` in the manifest; Maestro handles all resets automatically.

## Tasks

### Task 1: Load Latest Implementation

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

### Task 2: Run Tests

- [ ] **Determine test command** from I18N_SETUP.md:

```bash
TEST_CMD=$(grep "^**Command:**" {{AUTORUN_FOLDER}}/I18N_SETUP.md | head -1 | sed 's/\*\*Command:\*\* //')

if [ -z "$TEST_CMD" ]; then
  echo "No test command configured - skipping test verification"
  TEST_CMD="echo 'No tests configured'"
fi

echo "Test command: $TEST_CMD"
```

- [ ] **Run tests** to verify no breakage:

```bash
echo "Running tests..."
$TEST_CMD > {{AUTORUN_FOLDER}}/test_output_loop_{{LOOP_NUMBER}}.log 2>&1
TEST_EXIT_CODE=$?

if [ $TEST_EXIT_CODE -eq 0 ]; then
  echo "Tests passed"
else
  echo "Tests failed with exit code $TEST_EXIT_CODE"
  echo "Check {{AUTORUN_FOLDER}}/test_output_loop_{{LOOP_NUMBER}}.log for details"
fi
```

### Task 3: Verify Build

- [ ] **Run build verification** (if applicable):

```bash
if [ -f "go.mod" ]; then
  echo "Running Go build verification..."
  go build ./... 2>&1 | tee {{AUTORUN_FOLDER}}/build_output_loop_{{LOOP_NUMBER}}.log
  BUILD_EXIT_CODE=$?
elif [ -f "package.json" ]; then
  echo "Running npm build verification..."
  npm run build 2>&1 | tee {{AUTORUN_FOLDER}}/build_output_loop_{{LOOP_NUMBER}}.log
  BUILD_EXIT_CODE=$?
elif [ -f "Makefile" ]; then
  echo "Running make build..."
  make build 2>&1 | tee {{AUTORUN_FOLDER}}/build_output_loop_{{LOOP_NUMBER}}.log
  BUILD_EXIT_CODE=$?
else
  echo "No build system detected - skipping build verification"
  BUILD_EXIT_CODE=0
fi

if [ $BUILD_EXIT_CODE -eq 0 ]; then
  echo "Build verification passed"
else
  echo "Build failed with exit code $BUILD_EXIT_CODE"
fi
```

### Task 4: Update Test Results

- [ ] **Update TEST_RESULTS.md** — Append to (or create) `{{AUTORUN_FOLDER}}/TEST_RESULTS.md`:

```markdown
## Loop {{LOOP_NUMBER}} - {{DATE}}

**File Processed:** [from latest FILE_N_FIXES.md]

### Test Results
- **Command:** `$TEST_CMD`
- **Exit Code:** $TEST_EXIT_CODE
- **Result:** [PASS | FAIL]
- **Output:** See `test_output_loop_{{LOOP_NUMBER}}.log`

### Build Verification
- **Exit Code:** $BUILD_EXIT_CODE
- **Result:** [PASS | FAIL]
- **Output:** See `build_output_loop_{{LOOP_NUMBER}}.log`

### Overall Status: [VERIFIED | FAILED | NEEDS REVIEW]

---
```

### Task 5: Handle Failures

- [ ] **If verification failed**: If either tests or build failed:
  1. Review the failure logs
  2. Determine if it's related to i18n changes or pre-existing
  3. Update VIOLATIONS_PLAN.md to mark the file as `MANUAL_REVIEW` if needed
  4. Add details to MANUAL_REVIEW.md explaining the failure

### Task 6: Loop Gate Check

- [ ] **Decide: continue or exit**. Read `{{AUTORUN_FOLDER}}/VIOLATIONS_PLAN.md` and count files by status.

  Display a summary: (1) COMPLETE count, (2) PENDING count, (3) MANUAL_REVIEW count, (4) decision.

  - **If PENDING > 0**: Log "Continuing — N files remaining." The loop auto-continues via `resetOnCompletion`.
  - **If PENDING = 0**: Log "All files processed — exiting to finalization."

## Notes

- Test failures may indicate pre-existing issues unrelated to i18n changes — investigate before blaming the fix.
- Quality matters more than 100% coverage — document skip/manual-review decisions for future reference.
- Maestro handles loop resets automatically via `resetOnCompletion: true`. No manual reset tasks are needed.
