# Verify Progress - i18n Cleanup Gate

## Context

- **Playbook:** Internationalization Cleanup
- **Agent:** {{AGENT_NAME}}
- **Project:** {{AGENT_PATH}}
- **Loop:** {{LOOP_NUMBER}}
- **Date:** {{DATE}}
- **Working Folder:** {{AUTORUN_FOLDER}}

## Purpose

This document verifies the most recent file's i18n fixes with tests and build checks, then determines whether to continue looping or exit to finalization. **This is the only document with Reset ON** - it controls loop continuation by resetting Document 4 when more work remains.

## Instructions

1. **Review the latest implementation** from FILE_N_FIXES.md files
2. **Run tests** to verify no breakage
3. **Run build verification** to ensure compilation
4. **Update TEST_RESULTS.md** with verification output
5. **Check for remaining PENDING files** in VIOLATIONS_PLAN.md
6. **If PENDING files exist**: Reset Document 4 to continue the loop
7. **If NO PENDING files**: Do NOT reset - pipeline exits to Document 6

## Verification Tasks

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

## Loop Gate Check

- [ ] **Check for remaining files and decide**: Read `{{AUTORUN_FOLDER}}/VIOLATIONS_PLAN.md` and check for files with status `PENDING`. The loop should CONTINUE (reset Document 4 below) if there are items with status `PENDING`. The loop should EXIT (do NOT reset Document 4) when there are no `PENDING` files remaining. Display a summary showing: (1) number of COMPLETE files, (2) number of PENDING files, (3) number of manual review items, and (4) the decision to CONTINUE or EXIT.

## Reset Task (Only if PENDING files remain)

If the loop gate check above determines we need to continue (PENDING files exist), reset Document 4 to process the next file:

- [ ] **Reset 4_IMPLEMENT.md**: Uncheck all tasks in `{{AUTORUN_FOLDER}}/4_IMPLEMENT.md`

**IMPORTANT**: Only reset Document 4 if there are PENDING files remaining in VIOLATIONS_PLAN.md. If all files are COMPLETE, SKIP, or MANUAL_REVIEW, leave this reset task unchecked to allow the pipeline to exit to Document 6.

## Decision Logic

```
IF VIOLATIONS_PLAN.md contains files with status "PENDING":
    → Reset Document 4 (CONTINUE TO PROCESS NEXT FILE)

ELSE IF all files are COMPLETE, SKIP, or MANUAL_REVIEW:
    → Do NOT reset anything (ALL WORK COMPLETE - EXIT TO FINALIZE)
```

## How This Works

This document controls loop continuation through resets:
- **Reset task checked** → Document 4 gets reset → Loop continues
- **Reset task unchecked** → Nothing gets reset → Pipeline exits to Document 6
- **Document 5 auto-resets** via Maestro's `resetOnCompletion: true` - no manual reset needed

### Exit Conditions (Do NOT Reset)

Exit when ALL of these are true:
1. **All files processed**: No files with status `PENDING` in VIOLATIONS_PLAN.md
2. **Verification passed**: Tests and build succeeded (or failures addressed)

Also exit if:
3. **Max Loops**: Hit the loop limit (20) in Batch Runner

### Continue Conditions (Reset Document 4)

Continue if:
1. There are files with status exactly `PENDING` in VIOLATIONS_PLAN.md
2. We haven't hit max loops

## Current Status

Before making a decision, check the progress:

| Category | Count |
|----------|-------|
| **COMPLETE files** | ___ |
| **PENDING files** | ___ |
| **SKIP files** | ___ |
| **MANUAL_REVIEW files** | ___ |
| **Manual review items** | ___ |
| **Tests passed?** | YES / NO |
| **Build passed?** | YES / NO |

## Progress History

Track progress across loops:

| Loop | Files Complete | Files Pending | Strings Fixed | Decision |
|------|----------------|---------------|---------------|----------|
| 1 | ___ | ___ | ___ | [CONTINUE / EXIT] |
| 2 | ___ | ___ | ___ | [CONTINUE / EXIT] |
| ... | ... | ... | ... | ... |

## Manual Override

**To force exit early:**
- Leave the reset task unchecked regardless of remaining PENDING files
- Document justification in VIOLATIONS_PLAN.md

**To continue past manual review items:**
- Check the reset task to keep processing
- Will loop back to find next PENDING file

**To pause for review:**
- Leave reset task unchecked
- Review MANUAL_REVIEW.md and TEST_RESULTS.md
- Address flagged items
- Restart when ready

## Remaining Work Summary

Items that still need attention after this loop:

### Needs Manual Review
- [ ] [Filename]: [reason for manual review]

### Test/Build Failures
- [ ] [Filename]: [what failed and why]

### Skipped Files
- [ ] [Filename]: [reason for skip]

## Notes

- The goal is **zero hard-coded strings** in production code
- Some strings may be acceptable to skip (debug output, internal tools)
- Test failures may indicate pre-existing issues unrelated to i18n changes
- Quality matters more than hitting 100% - some edge cases are acceptable
- Document all skip/manual-review decisions for future reference
