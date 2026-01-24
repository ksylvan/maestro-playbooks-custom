# Analyze i18n Setup

## Context

- **Playbook:** Internationalization Cleanup
- **Agent:** {{AGENT_NAME}}
- **Project:** {{AGENT_PATH}}
- **Loop:** {{LOOP_NUMBER}}
- **Date:** {{DATE}}
- **Working Folder:** {{AUTORUN_FOLDER}}

## Purpose

Auto-detect the project's internationalization pattern, identify all supported languages, and understand the structure for adding new translation keys.

## Tasks

- [ ] **Read configuration** from previous document:

```bash
cat {{AUTORUN_FOLDER}}/I18N_CONTEXT.md
```

- [ ] **Discover i18n pattern** - Analyze the codebase to understand how i18n is implemented:

Search for common patterns:
1. **Go projects**: Look for `embed.FS` with JSON/YAML translations, or libraries like `go-i18n`
2. **JavaScript/TypeScript**: Check for i18next, react-intl, vue-i18n, formatjs
3. **Python**: Look for gettext `.po` files, babel configuration
4. **Ruby**: Check for Rails i18n YAML files
5. **Generic**: Search for translation files (*.json, *.yaml in i18n/locale/translations folders)

Examine:
- Directory structure for translation files
- Code examples showing how strings are translated
- Key naming conventions (dot notation, underscores, etc.)
- Supported languages (by finding all translation file variants)

- [ ] **Identify supported languages** - Find all language codes currently supported:

```bash
# This will vary by project structure - examples:
# Go: ls -1 i18n/ or embedded directories
# JS: ls -1 public/locales/ or src/i18n/
# Python: ls -1 locale/
# Generic: find . -type d -name "translations" -o -name "i18n" -o -name "locale"

echo "Discovering supported languages..."
# The actual command depends on the detected i18n pattern
```

- [ ] **Understand key structure** - Document the naming convention for i18n keys:

Examples to look for:
- Flat: `"error_invalid_input"`
- Namespaced: `"errors.validation.invalid_input"`
- Hierarchical JSON: `{ "errors": { "validation": { "invalid_input": "..." } } }`

- [ ] **Find translation files** - Document where translation files are located and how they're organized:

```bash
# Find all translation files
echo "Locating translation files..."
# Examples:
# find . -name "*.i18n.json"
# find . -path "*/translations/*.json"
# find . -name "messages.json" -o -name "en.json"
```

- [ ] **Determine test command** - Discover how to run tests for this project:

First check documentation:

```bash
# Check for test guidance in documentation
echo "Checking documentation for test commands..."

for file in CONTRIBUTING.md README.md CLAUDE.md AGENTS.md; do
  if [ -f "$file" ]; then
    echo "=== Checking $file ==="
    # Look for test command patterns
    grep -i -A 3 "test\|testing\|run.*test" "$file" | head -20
  fi
done
```

Then use heuristics if not found:

```bash
# Heuristic fallbacks
if [ -f "go.mod" ]; then
  echo "Detected Go project → test command: go test ./..."
elif [ -f "package.json" ]; then
  echo "Detected Node project → checking package.json scripts"
  grep "\"test\"" package.json
elif [ -f "pytest.ini" ] || grep -q "pytest" pyproject.toml 2>/dev/null; then
  echo "Detected Python/pytest → test command: pytest"
elif [ -f "Gemfile" ]; then
  echo "Detected Ruby project → test command: bundle exec rspec"
else
  echo "⚠️  Could not auto-detect test command. Please configure in I18N_CONTEXT.md"
fi
```

- [ ] **Create analysis report** - Document all findings:

Create `{{AUTORUN_FOLDER}}/I18N_SETUP.md`:

```markdown
# i18n Setup Analysis

## Detected Pattern

**Type:** [Go embed | i18next | gettext | Rails i18n | etc.]

**Description:**
[How the i18n system works in this codebase - include code examples]

Example of proper i18n usage:
\`\`\`[language]
// Show actual code from the project demonstrating correct i18n usage
\`\`\`

## Supported Languages

| Language Code | Language Name | Translation File |
|---------------|---------------|------------------|
| en | English | [path to English translations] |
| es | Spanish | [path to Spanish translations] |
| fr | French | [path to French translations] |
| ... | ... | ... |

**Total Languages:** [count]

## Key Structure

**Convention:** [flat | dot-notation | hierarchical JSON | etc.]

**Examples:**
- Error messages: `errors.validation.invalid_input` or `error_invalid_input`
- UI labels: `ui.buttons.submit` or `button_submit`
- Log messages: `logs.startup.server_ready` or `log_server_ready`

**Namespace Guidelines:**
[Document any project-specific conventions for organizing keys]

## Translation File Locations

**Base Directory:** [e.g., `i18n/`, `public/locales/`, `locale/`]

**File Pattern:** [e.g., `{lang}.json`, `messages_{lang}.properties`, `{lang}/LC_MESSAGES/`]

**File Structure:**
\`\`\`json
{
  "example": "of file structure"
}
\`\`\`

## Adding New Keys

**Process:**
1. [Step-by-step instructions for adding new i18n keys to this project]
2. [How to update all language files]
3. [Any special considerations - pluralization, interpolation syntax, etc.]

**Code Change Pattern:**

Before (hard-coded):
\`\`\`[language]
// Show example of hard-coded string
\`\`\`

After (i18n):
\`\`\`[language]
// Show how to replace with i18n call
\`\`\`

## Test Command

**Command:** [discovered test command]

**Source:** [Documentation file | Heuristic | Manual configuration]

## Build Command

**Command:** [discovered build command, if applicable]

## Notes

[Any special considerations, edge cases, or warnings about this i18n system]
```

- [ ] **Validate completeness** - Ensure all necessary information was captured:

```bash
echo "✅ i18n setup analysis complete"
echo "✅ I18N_SETUP.md created in {{AUTORUN_FOLDER}}"
echo "✅ Ready to proceed to Document 3: Find Violations"
```
