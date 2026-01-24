# Establish Context

## Context

- **Playbook:** Internationalization Cleanup
- **Agent:** {{AGENT_NAME}}
- **Project:** {{AGENT_PATH}}
- **Loop:** {{LOOP_NUMBER}}
- **Date:** {{DATE}}
- **Working Folder:** {{AUTORUN_FOLDER}}

## Purpose

Configure the i18n cleanup scope, verify branch safety, and establish baseline context for the analysis phase.

## Tasks

- [ ] **Check current git branch** and verify it is NOT `main`:

```bash
CURRENT_BRANCH=$(git branch --show-current)
if [ "$CURRENT_BRANCH" = "main" ]; then
  echo "❌ ERROR: Cannot run on 'main' branch. Please create a feature branch first."
  echo "Example: git checkout -b fix/i18n-cleanup"
  exit 1
fi
echo "✅ Branch check passed: $CURRENT_BRANCH"
```

- [ ] **Check git status** to document current state:

```bash
git status
```

- [ ] **Configure scan scope** - By default, scan the entire repository. User can modify this to a specific directory:

Create `{{AUTORUN_FOLDER}}/I18N_CONTEXT.md`:

```markdown
# i18n Cleanup Context

## Configuration

- **Scan Scope:** `.` (entire repository)
  - Modify this to target specific directories (e.g., `plugins/`, `src/`, `lib/`)
  - Can be comma-separated list: `plugins/,patterns/`

- **Test Command:** auto-detect
  - Will check documentation files (CONTRIBUTING.md, README.md, CLAUDE.md, AGENTS.md) first
  - Falls back to heuristics based on project files
  - Override here if needed: `go test ./...`, `npm test`, `pytest`, etc.

- **Manual Review Threshold:** medium
  - Strings with interpolations or context-dependent meaning will be flagged
  - Options: `low` (flag very few), `medium` (balanced), `high` (flag many)

## Git Context

- **Branch:** {{GIT_BRANCH}}
- **Date:** {{DATE}}
- **Agent:** {{AGENT_NAME}}
- **Project:** {{AGENT_PATH}}

## Status

✅ Configuration ready for analysis phase
```

- [ ] **Verify project has i18n system** - Check for common i18n patterns:

```bash
# Check for various i18n patterns
echo "Checking for i18n patterns in project..."

if [ -f "go.mod" ]; then
  echo "Go project detected - checking for embed i18n files"
  find . -name "translations" -o -name "i18n" -o -name "locale" | head -5
fi

if [ -f "package.json" ]; then
  echo "Node project detected - checking for i18n libraries"
  grep -E "i18next|react-intl|vue-i18n|formatjs" package.json || echo "No common i18n libraries found"
fi

if [ -f "pyproject.toml" ] || [ -f "setup.py" ]; then
  echo "Python project detected - checking for gettext/babel"
  find . -name "*.po" -o -name "*.pot" -o -name "locale" | head -5
fi

echo "If no i18n system detected above, Document 2 will need to discover it."
```

- [ ] **Document baseline** - Confirm context is ready:

```bash
echo "✅ Context established successfully"
echo "✅ I18N_CONTEXT.md created in {{AUTORUN_FOLDER}}"
echo "✅ Ready to proceed to Document 2: Analyze i18n Setup"
```
