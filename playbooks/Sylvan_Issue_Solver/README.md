# Sylvan Issue Solver Playbook

A Maestro Auto Run playbook for implementing fixes and features from [ksylvan/sylvan-website](https://github.com/ksylvan/sylvan-website) GitHub issues.

## What This Playbook Does

Takes a GitHub issue, analyzes it, creates a phased implementation plan, and iteratively implements the solution with test verification at each step. Designed specifically for the Sylvan website built with Next.js, TypeScript, and deployed on Vercel.

### Key Features

- **Branch Safety**: Refuses to run on `main` or `develop` branches - requires a feature/fix branch
- **Hybrid Workflow**: Fixed analysis phase, then iterative implementation loop
- **Phased Execution**: Complex issues broken into sequential phases
- **Test Verification**: Runs `npm run test -- --run` and `npm run type-check` after each phase
- **Vercel Preview Deployment**: Pushes to trigger preview deployment before PR creation
- **PR Preparation**: Creates PR against `develop` branch (not main) with description and checklist

## Playbook Structure

This playbook uses a **hybrid pattern** with a fixed analysis phase and iterative implementation:

```
┌─────────────────────────────────────────────────────────────┐
│  ANALYSIS PHASE (runs once, persists via files)             │
├─────────────────────────────────────────────────────────────┤
│  1_ESTABLISH_CONTEXT.md  → Issue link, branch check         │
│  2_ANALYZE_ISSUE.md      → Root cause analysis              │
│  3_CREATE_PLAN.md        → Phased implementation            │
└─────────────────────────────────────────────────────────────┘
                        ↓
┌─────────────────────────────────────────────────────────────┐
│  IMPLEMENTATION LOOP (repeats until complete)               │
├─────────────────────────────────────────────────────────────┤
│  4_IMPLEMENT.md          → Execute one phase                │
│  5_PROGRESS.md           → Test, verify, loop gate          │
│         ↑                         │                         │
│         └── more phases ──────────┘                         │
│                                   ↓ (all complete)          │
│                     Finalize: E2E, Vercel, PR to develop    │
└─────────────────────────────────────────────────────────────┘
```

## Prerequisites

- Maestro installed and configured
- Node.js 20+ installed
- Sylvan website repository cloned
- **IMPORTANT**: Checked out to a feature/fix branch (NOT `main` or `develop`)
- GitHub CLI (`gh`) installed for issue fetching

## Sylvan Tech Stack

The playbook is tailored for Sylvan's architecture:

| Technology | Version | Purpose |
|------------|---------|---------|
| Next.js | 15+ | App Router framework |
| React | 19 | UI library |
| TypeScript | 5+ | Type safety |
| Tailwind CSS | 3+ | Styling |
| Vitest | - | Unit testing |
| Playwright | - | E2E testing |
| Vercel | - | Deployment |

## Setup in Maestro

1. Clone the Sylvan website repository
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
1. **Document 1** → Verifies branch safety (not `main` or `develop`), loads issue context
2. **Document 2** → Analyzes issue root cause, maps to Next.js architecture
3. **Document 3** → Creates phased implementation plan

### Implementation Loop (Repeats)
1. **Document 4** → Implements the next incomplete phase following Sylvan patterns
2. **Document 5** → Runs tests (`npm run test -- --run`, `npm run type-check`), verifies progress, resets docs 4-5 if more phases remain

### Finalization (Doc 5 exit path)
When all phases are COMPLETE, Document 5 runs E2E tests, pushes branch, waits for Vercel preview deployment, verifies the preview site, and creates PR against `develop`.

## Working Files Generated

| File | Purpose | Persists Across Loops |
|------|---------|----------------------|
| `ISSUE_CONTEXT.md` | Issue details and Sylvan context | Yes |
| `ISSUE_ANALYSIS.md` | Root cause and affected Next.js areas | Yes |
| `IMPLEMENTATION_PLAN.md` | Phased plan with status tracking | Yes (updated) |
| `PHASE_N_IMPLEMENTATION.md` | Details of each phase's changes | Yes |
| `TEST_RESULTS.md` | Latest test execution results | Updated each loop |
| `PR_DESCRIPTION.md` | Ready-to-use PR description | Final |
| `COMPLETION_SUMMARY.md` | Summary with next steps | Final |

## Branch Safety

**This playbook REFUSES to run on the `main` OR `develop` branches.**

Before any implementation:
1. Document 1 checks `git branch --show-current`
2. If on `main` or `develop`, playbook halts with instructions
3. User must create/checkout a feature branch first

This prevents accidental commits to protected branches.

## Vercel Deployment Flow

Document 5's exit path handles Vercel deployment:

1. **Push Branch** → Triggers Vercel preview deployment automatically
2. **Check Status** → Runs `npm run deploy-info` to get deployment status
3. **Wait for Ready** → Polls until preview deployment reaches "Ready" state
4. **Visual Verification** → Uses Chrome DevTools MCP to verify preview site
5. **Create PR** → Creates PR against `develop` branch (NOT main)

### Post-Merge Deployment Path

After playbook completion, the manual merge flow is:

```
feature branch → PR to develop → staging.sylvan.com
                                       ↓
                               verify staging
                                       ↓
                develop → PR to main → production
```

## Customization

Edit the issue URL in `1_ESTABLISH_CONTEXT.md` before running:

```markdown
**GitHub Issue**: https://github.com/ksylvan/sylvan-website/issues/XXXX
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
2. Implements it following Sylvan patterns
3. Runs tests (`npm run test -- --run`, `npm run type-check`, `npm run build`)
4. Updates phase status to `COMPLETE` or `FAILED`

## Exit Conditions

- **Success**: All phases complete, tests pass → Doc 5 runs finalization tasks
- **Max Loops**: Reached loop limit → exits with partial progress
- **Branch Error**: On `main` or `develop` branch → halts immediately
- **Test Failure**: Persistent test failures → marks phase as `BLOCKED`

## Support

For issues, refer to:
- Maestro: <https://github.com/pedramamini/Maestro>
- Sylvan Website: <https://github.com/ksylvan/sylvan-website>
