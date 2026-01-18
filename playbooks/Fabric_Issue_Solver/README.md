# Fabric Issue Solver Playbook

A Maestro Auto Run playbook for implementing fixes and features from [danielmiessler/fabric](https://github.com/danielmiessler/fabric) GitHub issues.

## What This Playbook Does

Takes a GitHub issue, analyzes it, creates a phased implementation plan, and iteratively implements the solution with test verification at each step.

### Key Features

- **Branch Safety**: Refuses to run on `main` branch - requires a feature/fix branch
- **Hybrid Workflow**: Fixed analysis phase, then iterative implementation loop
- **Phased Execution**: Complex issues broken into sequential phases
- **Test Verification**: Runs `go test ./...` after each implementation phase
- **PR Preparation**: Generates PR description and changelog when complete

## Playbook Structure

This playbook uses a **hybrid pattern** with a fixed analysis phase and iterative implementation:

```
┌─────────────────────────────────────────────────────┐
│  ANALYSIS PHASE (runs once, persists via files)    │
├─────────────────────────────────────────────────────┤
│  1_ESTABLISH_CONTEXT.md  → Issue link, branch check │
│  2_ANALYZE_ISSUE.md      → Root cause analysis      │
│  3_CREATE_PLAN.md        → Phased implementation    │
└─────────────────────────────────────────────────────┘
                        ↓
┌─────────────────────────────────────────────────────┐
│  IMPLEMENTATION LOOP (repeats until complete)       │
├─────────────────────────────────────────────────────┤
│  4_IMPLEMENT.md          → Execute one phase        │
│  5_VERIFY_PROGRESS.md    → Test & check progress    │
│         ↑                         │                 │
│         └── more phases ──────────┘                 │
└─────────────────────────────────────────────────────┘
                        ↓ (all phases complete)
┌─────────────────────────────────────────────────────┐
│  FINALIZATION (runs once)                           │
├─────────────────────────────────────────────────────┤
│  6_FINALIZE.md           → Prepare PR content       │
└─────────────────────────────────────────────────────┘
```

## Prerequisites

- Maestro installed and configured
- Go 1.24+ installed
- Fabric repository cloned
- **IMPORTANT**: Checked out to a feature/fix branch (NOT `main`)
- GitHub CLI (`gh`) installed for issue fetching

## Setup in Maestro

1. Clone the Fabric repository
2. Create and checkout a feature branch:
   ```bash
   git checkout -b fix/issue-XXXX
   # or
   git checkout -b feature/issue-XXXX
   ```
3. Place this playbook folder in your Maestro Auto Run directory
4. Update `1_ESTABLISH_CONTEXT.md` with the issue number
5. Configure Auto Run settings:
   - **Loop Mode**: ON (for implementation iteration)
   - **Max Loops**: 10 (reasonable limit)
6. Run the playbook

## Execution Flow

### Analysis Phase (Once)
1. **Document 1** → Verifies branch safety, loads issue context
2. **Document 2** → Analyzes issue root cause, identifies affected files
3. **Document 3** → Creates phased implementation plan

### Implementation Loop (Repeats)
4. **Document 4** → Implements the next incomplete phase
5. **Document 5** → Runs tests, verifies progress, triggers loop if more phases

### Finalization (Once)
6. **Document 6** → Generates PR description, changelog, and summary

## Working Files Generated

| File | Purpose | Persists Across Loops |
|------|---------|----------------------|
| `ISSUE_CONTEXT.md` | Issue details and background | Yes |
| `ISSUE_ANALYSIS.md` | Root cause and affected files | Yes |
| `IMPLEMENTATION_PLAN.md` | Phased plan with status tracking | Yes (updated) |
| `PHASE_N_IMPLEMENTATION.md` | Details of each phase's changes | Yes |
| `TEST_RESULTS.md` | Latest test execution results | Updated each loop |
| `PR_DESCRIPTION.md` | Ready-to-use PR description | Final |
| `CHANGELOG.md` | Generated changelog entry | Final |

## Branch Safety

**This playbook REFUSES to run on the `main` branch.**

Before any implementation:
1. Document 1 checks `git branch --show-current`
2. If on `main`, playbook halts with instructions
3. User must create/checkout a feature branch first

This prevents accidental commits to the main branch.

## Customization

Edit the issue URL in `1_ESTABLISH_CONTEXT.md` before running:

```markdown
**GitHub Issue**: https://github.com/danielmiessler/fabric/issues/XXXX
```

You can also add background context:

```markdown
## Additional Context
- Related discussions: [links]
- Slack/Discord conversations: [notes]
- Previous attempts: [description]
```

## Loop Behavior

The playbook loops through documents 4-5 until:
- All phases in `IMPLEMENTATION_PLAN.md` are marked `COMPLETE`
- Or max loops (10) is reached
- Or a phase fails verification repeatedly

Each loop iteration:
1. Finds the next `PENDING` phase
2. Implements it
3. Runs tests
4. Updates phase status to `COMPLETE` or `FAILED`

## Exit Conditions

- **Success**: All phases complete, tests pass → proceeds to Document 6
- **Max Loops**: Reached loop limit → exits with partial progress
- **Branch Error**: On `main` branch → halts immediately
- **Test Failure**: Persistent test failures → marks phase as `BLOCKED`

## Support

For issues, refer to:
- Maestro: https://github.com/pedramamini/Maestro
- Fabric: https://github.com/danielmiessler/fabric
