# Go-Specific i18n Pattern Fixes

> These patterns apply **only to Go projects**. Skip this file for JavaScript, Python, Ruby, and other languages.

## Pattern 1: fmt.Errorf with non-constant format string

Go's linter requires format strings to be constant. When using i18n.T() in fmt.Errorf, add a format specifier:

```go
// WRONG - triggers "non-constant format string in call to fmt.Errorf"
return fmt.Errorf(i18n.T("extension_registry_not_initialized"))

// CORRECT - add %s format specifier
return fmt.Errorf("%s", i18n.T("extension_registry_not_initialized"))
```

Apply this fix automatically - no human intervention needed. Search for all instances of `fmt.Errorf(i18n.T("..."))` and convert to `fmt.Errorf("%s", i18n.T("..."))`.

## Pattern 2: Whitespace and formatting in translations

Translation strings should contain ONLY the text content. All formatting characters (tabs, newlines, spaces) must stay in the source code.

```go
// WRONG - formatting inside translation
// Translation: "some_example_string" => "\t\t    This is an example\n\n"
// Code:
printf(i18n.T("some_example_string"))

// CORRECT - formatting in code, clean translation
// Translation: "some_example_string" => "This is an example"
// Code:
printf("\t\t    %s\n\n", i18n.T("some_example_string"))
```

**Apply this fix automatically**:
1. Extract leading/trailing whitespace and formatting from the original string
2. Create translation key with clean text only (no tabs, no leading/trailing spaces, no newlines)
3. Update code to include formatting characters outside the i18n.T() call using format specifiers

**More examples:**

```go
// Original code
fmt.Println("\n\nProcessing file: " + filename + "\n")

// WRONG translation
"processing_file" => "\n\nProcessing file: %s\n"
fmt.Println(i18n.T("processing_file", filename))

// CORRECT translation
"processing_file" => "Processing file: %s"
fmt.Println("\n\n" + i18n.T("processing_file", filename) + "\n")
// OR
fmt.Printf("\n\n%s\n", i18n.T("processing_file", filename))
```

```go
// Original code
log.Info("    [DEBUG] Starting operation...")

// WRONG translation
"debug_starting" => "    [DEBUG] Starting operation..."
log.Info(i18n.T("debug_starting"))

// CORRECT translation
"debug_starting" => "Starting operation..."
log.Info("    [DEBUG] " + i18n.T("debug_starting"))
```

**Key principle:** If whitespace/formatting is for layout/alignment, it belongs in code. If it's part of the actual message content, it can stay in the translation.
