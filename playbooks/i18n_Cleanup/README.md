# Internationalization Cleanup

## Overview

This playbook systematically identifies and fixes hard-coded strings in your codebase by replacing them with proper internationalization (i18n) patterns. It works with any project that has an i18n system, automatically detecting the pattern and generating translations for all supported languages.

## What It Does

1. **Analyzes** your project's i18n setup (Go embed, JSON files, gettext, React i18next, etc.)
2. **Scans** configured directories for hard-coded strings in:
   - Error messages
   - CLI output and help text
   - Log messages
   - UI labels and prompts
3. **Fixes** violations one file at a time by:
   - Extracting hard-coded strings
   - Generating contextual i18n keys
   - AI-translating for ALL supported languages
   - Updating source code to use i18n pattern
4. **Verifies** each change with tests and build checks
5. **Reports** unused i18n keys for cleanup

## Prerequisites

- Git repository with uncommitted changes allowed
- NOT on `main` branch (playbook will refuse to run)
- Working i18n system already in place
- Test suite or build system for verification

## How to Use

1. **Configure scope** in Document 1:
   - Set directory to scan (e.g., `plugins/`, `src/`, or `.` for entire repo)
   - Optionally specify test command (otherwise auto-detected)

2. **Run the playbook** with loop mode enabled

3. **Review** the generated files:
   - `VIOLATIONS_PLAN.md` - Prioritized list of files needing fixes
   - `MANUAL_REVIEW.md` - Complex strings flagged for human review
   - `UNUSED_KEYS.md` - Dead i18n keys that can be cleaned up

4. **Create PR** using generated `PR_DESCRIPTION.md`

## Loop Behavior

- **Analysis Phase (Documents 1-3)**: Runs once to analyze and find violations
- **Implementation Loop (Documents 4-6)**: Loops until all files are fixed
  - Fixes ONE file per iteration (Doc 4, `resetOnCompletion: true`)
  - Runs tests and build verification (Doc 5, `resetOnCompletion: true`)
  - Loop gate + finalization (Doc 6, `resetOnCompletion: false`) — reads `VIOLATIONS_PLAN.md`; unchecked = loop continues, checked = done then finalize
  - Maestro auto-resets docs 4-5 each iteration; only doc 6 (non-reset) drives loop continuation
- **Finalization (Doc 6)**: When no PENDING files remain, Doc 6 generates PR description, cleanup summary, and unstages tracking files

**Max Loops**: 20 (adjustable in manifest)

## Working Files Generated

| File | Purpose |
|------|---------|
| `I18N_CONTEXT.md` | Configuration and branch info |
| `I18N_SETUP.md` | Detected i18n pattern and supported languages |
| `VIOLATIONS_PLAN.md` | Files needing fixes with status tracking |
| `UNUSED_KEYS.md` | i18n keys defined but never used |
| `FILE_N_FIXES.md` | Details of each file's fixes |
| `MANUAL_REVIEW.md` | Strings needing human review |
| `TEST_RESULTS.md` | Test/build output from verification |
| `CLEANUP_SUMMARY.md` | Final summary of all changes |
| `PR_DESCRIPTION.md` | Ready-to-use PR description |

## Exit Conditions

The playbook exits when:
- All files are marked `COMPLETE` or `SKIP` in `VIOLATIONS_PLAN.md`
- Max loops (20) reached
- Critical test/build failures require manual intervention

## Example Use Cases

- **Fabric plugins**: Clean up recent plugins with hard-coded error messages
- **React apps**: Replace hard-coded UI text with i18next keys
- **CLI tools**: Internationalize help text and output messages
- **Python projects**: Migrate from hard-coded strings to gettext

## Tips

- **Start with focused scope**: Use `plugins/` or specific subdirectory for first run
- **Review manual items**: Some strings need context that AI can't infer
- **Run tests externally first**: Ensure test suite works before playbook runs it
- **Commit frequently**: Each loop creates a logical commit point
- **Check unused keys**: `UNUSED_KEYS.md` often reveals dead code worth removing

## Technical Details

**Supported i18n Patterns** (auto-detected):
- Go: `embed.FS` with JSON/YAML translations (see `assets/go-patterns.md` for Go-specific linter fixes)
- JavaScript: i18next, react-intl, vue-i18n
- Python: gettext, babel
- Ruby: Rails i18n, r18n
- Generic: JSON/YAML key-value files

**Test Command Discovery**:
1. Checks `CONTRIBUTING.md`, `README.md`, `CLAUDE.md`, `AGENTS.md` for test guidance
2. Falls back to heuristics:
   - `go.mod` → `go test ./...`
   - `package.json` → `npm test`
   - `pytest.ini` / `pyproject.toml` → `pytest`
   - `Gemfile` → `bundle exec rspec`

**String Detection Patterns**:
- Error creation: `fmt.Errorf()`, `errors.New()`, `throw new Error()`
- Output: `fmt.Println()`, `console.log()`, `print()`
- Logging: All standard logging libraries
- Templates: Hard-coded text in HTML/JSX

## Author

Kayvan Sylvan

## License

Same as parent repository
