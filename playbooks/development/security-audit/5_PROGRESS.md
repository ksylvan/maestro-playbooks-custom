# Security Audit Gate - CRITICAL/HIGH Remediation Target

## Context

- **Playbook:** Security
- **Agent:** {{AGENT_NAME}}
- **Project:** {{AGENT_PATH}}
- **Auto Run Folder:** {{AUTORUN_FOLDER}}
- **Loop:** {{LOOP_NUMBER}}

## Purpose

This document is the **security gate** for the audit pipeline. It checks whether all CRITICAL and HIGH severity vulnerabilities have been remediated. **This is the only document with Reset ON** - it controls loop continuation by resetting tasks in documents 1-4 when more work is needed.

## Instructions

1. **Read the plan** from `{{AUTORUN_FOLDER}}/LOOP_{{LOOP_NUMBER}}_PLAN.md`
2. **Check for remaining CRITICAL or HIGH severity items** that are still `PENDING` with EASY/MEDIUM remediability
3. **If such PENDING items exist**: Reset all tasks in documents 1-4 to continue the loop
4. **If NO such items exist**: Do NOT reset - pipeline exits

## Security Gate Check

- [ ] **Check for remaining vulnerabilities**: Read LOOP_{{LOOP_NUMBER}}_PLAN.md and LOOP_{{LOOP_NUMBER}}_VULNERABILITIES.md. The loop should CONTINUE (reset docs 1-4) if EITHER: (1) there are items with status `PENDING` that have CRITICAL or HIGH severity AND EASY or MEDIUM remediability, OR (2) VULNERABILITIES.md does NOT contain `## ALL_TACTICS_EXHAUSTED`. The loop should EXIT (do NOT reset) only when BOTH conditions are false: no PENDING CRITICAL/HIGH items with EASY/MEDIUM remediability AND all tactics are exhausted.

## Reset Tasks (Only if work remains)

If the security gate check above determines we need to continue (PENDING CRITICAL/HIGH items with EASY/MEDIUM remediability OR tactics remaining), reset all tasks in the following documents:

- [ ] **Reset 1_ANALYZE.md**: Uncheck all tasks in `{{AUTORUN_FOLDER}}/1_ANALYZE.md`
- [ ] **Reset 2_FIND_ISSUES.md**: Uncheck all tasks in `{{AUTORUN_FOLDER}}/2_FIND_ISSUES.md`
- [ ] **Reset 3_EVALUATE.md**: Uncheck all tasks in `{{AUTORUN_FOLDER}}/3_EVALUATE.md`
- [ ] **Reset 4_IMPLEMENT.md**: Uncheck all tasks in `{{AUTORUN_FOLDER}}/4_IMPLEMENT.md`

**IMPORTANT**: Only reset documents 1-4 if there is work remaining (PENDING CRITICAL/HIGH items with EASY/MEDIUM remediability OR unexplored tactics). If all tactics are exhausted AND all such items are IMPLEMENTED, WON'T DO, or PENDING - MANUAL REVIEW, leave these reset tasks unchecked to allow the pipeline to exit.

## Decision Logic

```
IF LOOP_{{LOOP_NUMBER}}_PLAN.md doesn't exist:
    → Do NOT reset anything (PIPELINE JUST STARTED - LET IT RUN)

ELSE IF items with status `PENDING` AND (CRITICAL|HIGH severity) AND (EASY|MEDIUM remediability) exist:
    → Reset documents 1-4 (CONTINUE TO IMPLEMENT PENDING ITEMS)

ELSE IF LOOP_{{LOOP_NUMBER}}_VULNERABILITIES.md does NOT contain "ALL_TACTICS_EXHAUSTED":
    → Reset documents 1-4 (CONTINUE TO DISCOVER MORE VULNERABILITIES)

ELSE:
    → Do NOT reset anything (ALL TACTICS EXHAUSTED AND NO PENDING CRITICAL/HIGH - EXIT)
```

**Key insight:** The loop should continue if EITHER:

1. There are PENDING CRITICAL/HIGH items with EASY/MEDIUM remediability to implement, OR
2. There are still tactics to execute (no `ALL_TACTICS_EXHAUSTED` marker)

## How This Works

This document controls loop continuation through resets:

- **Reset tasks checked** → Documents 1-4 get reset → Loop continues
- **Reset tasks unchecked** → Nothing gets reset → Pipeline exits

### Exit Conditions (Do NOT Reset)

Exit when ALL of these are true:

1. **Tactics exhausted**: `LOOP_{{LOOP_NUMBER}}_VULNERABILITIES.md` contains `## ALL_TACTICS_EXHAUSTED`
2. **No PENDING CRITICAL/HIGH**: All CRITICAL/HIGH items with EASY/MEDIUM remediability are `IMPLEMENTED`, `WON'T DO`, or `PENDING - MANUAL REVIEW`

Also exit if:
3. **Max Loops**: Hit the loop limit in Batch Runner

### Continue Conditions (Reset Documents 1-4)

Continue if EITHER is true:

1. There are items with status exactly `PENDING` that have CRITICAL/HIGH severity AND EASY/MEDIUM remediability
2. `LOOP_{{LOOP_NUMBER}}_VULNERABILITIES.md` does NOT contain `## ALL_TACTICS_EXHAUSTED` (more tactics to run)

## Current Status

Before making a decision, check the plan and vulnerabilities files:

| Category | Count |
|----------|-------|
| **CRITICAL - PENDING (EASY/MEDIUM)** | ___ |
| **CRITICAL - IMPLEMENTED** | ___ |
| **HIGH - PENDING (EASY/MEDIUM)** | ___ |
| **HIGH - IMPLEMENTED** | ___ |
| **MANUAL REVIEW (HARD)** | ___ |
| **WON'T DO / FALSE POSITIVE** | ___ |
| **Tactics Exhausted?** | YES / NO |

## Security Posture History

Track progress across loops:

| Loop | Critical (Start) | Critical (End) | High (Start) | High (End) | Decision |
|------|------------------|----------------|--------------|------------|----------|
| 1 | ___ | ___ | ___ | ___ | [CONTINUE / EXIT] |
| 2 | ___ | ___ | ___ | ___ | [CONTINUE / EXIT] |
| ... | ... | ... | ... | ... | ... |

## Manual Override

**To force exit early:**

- Leave all reset tasks unchecked regardless of remaining issues
- Document justification in SECURITY_LOG

**To continue fixing MEDIUM severity:**

- Check the reset tasks even when no CRITICAL/HIGH remain
- Will find and fix MEDIUM issues next loop

**To pause for manual review:**

- Leave unchecked
- Review SECURITY_LOG and plan
- Address MANUAL REVIEW items
- Restart when ready

## Remaining Work Summary

Items that still need attention after this loop:

### Needs Manual Review

- [ ] SEC-XXX: [description] - [why manual review needed]

### Accepted Risks

- [ ] SEC-XXX: [description] - [risk acceptance justification]

### Blocked / Waiting

- [ ] SEC-XXX: [description] - [what it's waiting for]

## Notes

- The goal is **zero CRITICAL and HIGH** findings
- MEDIUM severity items are defense-in-depth improvements
- Some findings may be false positives - verify before dismissing
- Document all risk acceptances for audit purposes
- Rotate any exposed credentials even after removing from code
