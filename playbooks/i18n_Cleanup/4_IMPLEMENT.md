# Implement i18n Fixes

## Context

- **Playbook:** Internationalization Cleanup
- **Agent:** {{AGENT_NAME}}
- **Project:** {{AGENT_PATH}}
- **Loop:** {{LOOP_NUMBER}}
- **Date:** {{DATE}}
- **Working Folder:** {{AUTORUN_FOLDER}}

## Purpose

Fix i18n violations in **ONE file per loop iteration** by extracting hard-coded strings, generating i18n keys, AI-translating for all supported languages, and updating source code.

## Tasks

- [ ] **Read current state**:

```bash
cat {{AUTORUN_FOLDER}}/I18N_SETUP.md
cat {{AUTORUN_FOLDER}}/VIOLATIONS_PLAN.md | grep -A 5 "PENDING" | head -20
```

- [ ] **Select next file** - Pick the first file with PENDING status:

```bash
# Extract the next PENDING file from VIOLATIONS_PLAN.md
NEXT_FILE=$(grep "| PENDING |" {{AUTORUN_FOLDER}}/VIOLATIONS_PLAN.md | head -1 | awk -F'|' '{print $3}' | xargs)

if [ -z "$NEXT_FILE" ]; then
  echo "✅ No more PENDING files - all violations addressed"
  exit 0
fi

echo "📝 Processing file: $NEXT_FILE"
```

- [ ] **Update status to IN_PROGRESS** in VIOLATIONS_PLAN.md:

```bash
# Update the status for the file we're about to process using awk
awk -F'|' -v file="$NEXT_FILE" 'BEGIN {OFS="|"}
  $3 ~ file && $6 ~ /PENDING/ {$6 = " IN_PROGRESS "; print; next}
  {print}' {{AUTORUN_FOLDER}}/VIOLATIONS_PLAN.md > {{AUTORUN_FOLDER}}/VIOLATIONS_PLAN.md.tmp && \
  mv {{AUTORUN_FOLDER}}/VIOLATIONS_PLAN.md.tmp {{AUTORUN_FOLDER}}/VIOLATIONS_PLAN.md

# Verify the update
echo "Status updated to IN_PROGRESS for: $NEXT_FILE"
grep "$NEXT_FILE" {{AUTORUN_FOLDER}}/VIOLATIONS_PLAN.md
```

- [ ] **Read the file** and identify all hard-coded strings:

```bash
cat "$NEXT_FILE"
```

Identify all instances of:
- Error creation with string literals
- Print/log statements with string literals
- UI text in templates
- Hard-coded help text

For each hard-coded string, note:
- Line number
- Type (error, output, log, UI label)
- Context (surrounding code)
- Any interpolation/formatting

- [ ] **Generate i18n keys** - Create descriptive, namespaced keys:

For each string, generate a key following the convention in I18N_SETUP.md.

**Key naming strategy:**
- Use file/module path as namespace: `plugins.youtube.errors.invalid_url`
- Be descriptive but concise: `invalid_url` not `error_message_for_invalid_url`
- Group by category: `errors.*`, `ui.*`, `logs.*`, `help.*`
- Avoid collisions: Check existing keys in translation files

**Example mapping:**

```
File: plugins/youtube/main.go
Line 45: return fmt.Errorf("invalid YouTube URL")
Key: plugins.youtube.errors.invalid_url

File: plugins/youtube/main.go
Line 78: fmt.Println("Fetching video metadata...")
Key: plugins.youtube.ui.fetching_metadata
```

- [ ] **AI-generate translations** - For each string, create translations for ALL supported languages:

Using the supported languages from I18N_SETUP.md, generate contextual translations.

**Translation guidelines:**
- Preserve placeholders: `%s`, `%d`, `{0}`, etc. must appear in translations
- Maintain technical terms: API names, protocols, etc. often don't translate
- Consider context: "file" (document) vs "file" (tool/rasp) in different languages
- Use formal/informal tone consistent with existing translations
- Verify plural forms if language has complex plural rules

**Example output:**

```json
{
  "plugins.youtube.errors.invalid_url": {
    "en": "Invalid YouTube URL",
    "es": "URL de YouTube no válida",
    "fr": "URL YouTube invalide",
    "de": "Ungültige YouTube-URL",
    "ja": "無効なYouTube URL",
    "zh": "无效的YouTube网址"
  }
}
```

- [ ] **Flag complex strings** - Identify strings needing manual review:

Append to `{{AUTORUN_FOLDER}}/MANUAL_REVIEW.md` for strings that:
- Have complex interpolation with business logic
- Are context-dependent (same English, different meanings)
- Mix technical and natural language
- Have unusual formatting requirements

Format:
```markdown
### File: [filename], Line: [line number]

**Original:** `[string]`

**Proposed Key:** `[key]`

**Issue:** [Why it needs review - e.g., "Context-dependent: could mean X or Y depending on usage"]

**Suggested Translations:**
- en: ...
- es: ...

**Action Needed:** [What human reviewer should verify]

---
```

- [ ] **Update translation files** - Add new keys to all language files:

Based on the structure in I18N_SETUP.md:
1. Read each language's translation file
2. Add new keys in appropriate namespace location
3. Preserve existing formatting and structure
4. Maintain alphabetical ordering if used
5. Validate JSON/YAML syntax

- [ ] **Update source code** - Replace hard-coded strings with i18n calls:

For each hard-coded string, replace with the appropriate i18n function call.

**Examples by language:**

Go:
```go
// Before
return fmt.Errorf("invalid YouTube URL")

// After
return fmt.Errorf(i18n.T("plugins.youtube.errors.invalid_url"))
```

JavaScript (i18next):
```javascript
// Before
throw new Error("Invalid YouTube URL");

// After
throw new Error(i18n.t("plugins.youtube.errors.invalid_url"));
```

Python (gettext):
```python
# Before
raise ValueError("Invalid YouTube URL")

# After
raise ValueError(_("plugins.youtube.errors.invalid_url"))
```

Ensure:
- Imports are added if needed
- Formatting/interpolation is preserved correctly
- Code still compiles/runs

- [ ] **Apply language-specific pattern fixes** - Read `I18N_SETUP.md` to determine the project language. If the project uses Go, read and apply the patterns from `playbooks/i18n_Cleanup/assets/go-patterns.md` (handles `fmt.Errorf` linter issues and whitespace-in-translations). For other languages, check for similar linter/formatter requirements and apply fixes as needed.

- [ ] **Create implementation log** for this file:

Create `{{AUTORUN_FOLDER}}/FILE_{{LOOP_NUMBER}}_FIXES.md`:

```markdown
# File Implementation: [filename]

**Loop:** {{LOOP_NUMBER}}
**Date:** {{DATE}}
**File:** [full path]

## Violations Fixed

| Line | Original String | i18n Key | Type |
|------|----------------|----------|------|
| 45 | "invalid YouTube URL" | plugins.youtube.errors.invalid_url | Error |
| 78 | "Fetching video metadata..." | plugins.youtube.ui.fetching_metadata | UI |
| ... | ... | ... | ... |

**Total strings fixed:** [count]

## Translation Keys Added

| Key | Languages | Flagged for Review? |
|-----|-----------|---------------------|
| plugins.youtube.errors.invalid_url | en, es, fr, de, ja, zh | No |
| plugins.youtube.ui.fetching_metadata | en, es, fr, de, ja, zh | No |

## Code Changes

- Updated [count] locations in source code
- Added imports: [if applicable]
- Translation files updated: [count] files

## Manual Review Items

[If any - reference MANUAL_REVIEW.md]

## Status

✅ COMPLETE - Ready for verification in Document 5
```

- [ ] **Update VIOLATIONS_PLAN.md** - Mark file as COMPLETE:

```bash
# Update the status to COMPLETE for the file we just processed using awk
awk -F'|' -v file="$NEXT_FILE" 'BEGIN {OFS="|"}
  $3 ~ file && $6 ~ /IN_PROGRESS/ {$6 = " COMPLETE "; print; next}
  {print}' {{AUTORUN_FOLDER}}/VIOLATIONS_PLAN.md > {{AUTORUN_FOLDER}}/VIOLATIONS_PLAN.md.tmp && \
  mv {{AUTORUN_FOLDER}}/VIOLATIONS_PLAN.md.tmp {{AUTORUN_FOLDER}}/VIOLATIONS_PLAN.md

# Verify the update
echo "Status updated to COMPLETE for: $NEXT_FILE"
grep "$NEXT_FILE" {{AUTORUN_FOLDER}}/VIOLATIONS_PLAN.md

# Verify PENDING count
REMAINING=$(grep -c "| PENDING |" {{AUTORUN_FOLDER}}/VIOLATIONS_PLAN.md || echo 0)
echo "Remaining PENDING files: $REMAINING"
```

- [ ] **Validate changes**:

```bash
echo "✅ File processed: $NEXT_FILE"
echo "✅ Translation keys added to all language files"
echo "✅ Source code updated with i18n calls"
echo "✅ FILE_{{LOOP_NUMBER}}_FIXES.md created"
echo "✅ VIOLATIONS_PLAN.md updated to COMPLETE"
echo "✅ Ready for verification in Document 5"
```

## Notes

This document processes **ONE file per loop iteration** to keep commits atomic and reviewable. Document 5 will verify the changes with tests and determine whether to continue looping.
