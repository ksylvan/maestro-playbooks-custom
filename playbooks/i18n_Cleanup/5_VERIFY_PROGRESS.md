# Verify Progress

## Context

- **Playbook:** Internationalization Cleanup
- **Agent:** {{AGENT_NAME}}
- **Project:** {{AGENT_PATH}}
- **Loop:** {{LOOP_NUMBER}}
- **Date:** {{DATE}}
- **Working Folder:** {{AUTORUN_FOLDER}}

## Purpose

Verify the most recent file's i18n fixes with tests and build checks, then determine whether to continue looping or exit to finalization.

**CRITICAL:** This document has `resetOnCompletion: true` and controls the loop.

## Tasks

- [ ] **Read latest implementation**:

```bash
# Find the most recent FILE_N_FIXES.md
LATEST_FIX=$(ls -t {{AUTORUN_FOLDER}}/FILE_*_FIXES.md 2>/dev/null | head -1)

if [ -n "$LATEST_FIX" ]; then
  echo "=== Latest Implementation ==="
  cat "$LATEST_FIX"
else
  echo "No implementation files found yet - analysis phase complete"
fi
```

- [ ] **Determine test command** from I18N_SETUP.md:

```bash
TEST_CMD=$(grep "^**Command:**" {{AUTORUN_FOLDER}}/I18N_SETUP.md | head -1 | sed 's/\*\*Command:\*\* //')

if [ -z "$TEST_CMD" ]; then
  echo "⚠️  No test command configured - skipping test verification"
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
  echo "✅ Tests passed"
else
  echo "❌ Tests failed with exit code $TEST_EXIT_CODE"
  echo "Check {{AUTORUN_FOLDER}}/test_output_loop_{{LOOP_NUMBER}}.log for details"
fi
```

- [ ] **Run build verification** (if applicable):

```bash
# Detect build command
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
  echo "✅ Build verification passed"
else
  echo "❌ Build failed with exit code $BUILD_EXIT_CODE"
fi
```

- [ ] **Update TEST_RESULTS.md** with verification output:

Append to (or create) `{{AUTORUN_FOLDER}}/TEST_RESULTS.md`:

```markdown
## Loop {{LOOP_NUMBER}} - {{DATE}}

**File Processed:** [from latest FILE_N_FIXES.md]

### Test Results

**Command:** `$TEST_CMD`

**Exit Code:** $TEST_EXIT_CODE

**Result:** [PASS | FAIL]

**Output:** See `test_output_loop_{{LOOP_NUMBER}}.log`

### Build Verification

**Exit Code:** $BUILD_EXIT_CODE

**Result:** [PASS | FAIL]

**Output:** See `build_output_loop_{{LOOP_NUMBER}}.log`

### Overall Status

[✅ VERIFIED | ❌ FAILED | ⚠️ NEEDS REVIEW]

---
```

- [ ] **Handle test/build failures** - If verification failed:

If either tests or build failed:

1. Review the failure logs
2. Determine if it's related to i18n changes or pre-existing
3. Update VIOLATIONS_PLAN.md to mark the file as MANUAL_REVIEW if needed
4. Add details to MANUAL_REVIEW.md explaining the failure

```bash
if [ $TEST_EXIT_CODE -ne 0 ] || [ $BUILD_EXIT_CODE -ne 0 ]; then
  echo "⚠️  Verification failed - may need manual intervention"
  echo "Consider marking last file as MANUAL_REVIEW in VIOLATIONS_PLAN.md"
fi
```

- [ ] **Check for remaining work** - Determine if loop should continue:

```bash
# Count PENDING files in violations plan
PENDING_COUNT=$(grep -c "| PENDING |" {{AUTORUN_FOLDER}}/VIOLATIONS_PLAN.md || echo 0)

echo "Files remaining: $PENDING_COUNT"
```

- [ ] **Loop control decision**:

```bash
if [ $PENDING_COUNT -gt 0 ]; then
  echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
  echo "🔄 CONTINUE LOOP: $PENDING_COUNT files still PENDING"
  echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
  echo ""
  echo "Resetting to Document 4 to process next file..."
  echo ""
  # Document will auto-reset due to resetOnCompletion: true
else
  echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
  echo "✅ EXIT LOOP: All files processed"
  echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
  echo ""
  echo "Proceeding to Document 6: Finalize"
  echo ""
  # Document will NOT reset, allowing progression to Document 6
fi
```

- [ ] **Create progress summary**:

```bash
echo "=== Loop {{LOOP_NUMBER}} Summary ==="
echo "Completed files: $(grep -c '| COMPLETE |' {{AUTORUN_FOLDER}}/VIOLATIONS_PLAN.md || echo 0)"
echo "Pending files: $PENDING_COUNT"
echo "Manual review items: $(grep -c '^###' {{AUTORUN_FOLDER}}/MANUAL_REVIEW.md || echo 0)"
echo ""

if [ $PENDING_COUNT -eq 0 ]; then
  echo "✅ All violations addressed - ready for finalization"
fi
```

## Loop Control Logic

```
IF no FILE_N_FIXES.md exists yet:
    → Analysis phase just completed, proceed to implementation

ELSE IF files with status "PENDING" exist in VIOLATIONS_PLAN.md:
    → Continue loop (this document resets, returns to Document 4)

ELSE IF test/build failures need manual intervention:
    → Can mark as MANUAL_REVIEW and continue, or exit

ELSE (no PENDING files):
    → Exit loop (this document does NOT reset, proceeds to Document 6)
```

## Notes

This document acts as the **loop gate**. Its `resetOnCompletion: true` setting causes Documents 4-5 to repeat when PENDING work remains.
