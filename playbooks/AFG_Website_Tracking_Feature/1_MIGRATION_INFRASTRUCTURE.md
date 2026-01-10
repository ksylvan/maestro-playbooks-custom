# Document 1: Create Migration Infrastructure

## Context

- **Playbook**: AFG Activity Tracking with Auto-Migration System
- **Agent**: {{AGENT_NAME}}
- **Project**: {{AGENT_PATH}}
- **Date**: {{DATE}}
- **Working Folder**: {{AUTORUN_FOLDER}}

## Purpose

Create a Django/Rails-style auto-migration system that automatically runs database migrations on every deployment. This infrastructure will track which migrations have been applied and run pending migrations in numerical order.

## Overview

This document creates the foundation for automatic migrations that will:
- Track applied migrations in a `_migrations` database table
- Scan for numbered migration files in `src/db/migrations/`
- Run only new migrations (skip already-applied ones)
- Integrate into `init.ts` to run automatically on deployment
- Be safe to run multiple times (idempotent)

## Tasks

### Task 1: Create Migration Runner

- [ ] **Create migration-runner.ts**: Create a new file at `{{AGENT_PATH}}/src/db/migration-runner.ts` with the following complete implementation:

```typescript
import * as fs from "node:fs";
import * as path from "node:path";
import type { VercelPoolClient } from "@vercel/postgres";

/**
 * Migration file structure
 */
export interface Migration {
 name: string;
 up: (client: VercelPoolClient) => Promise<void>;
 down?: (client: VercelPoolClient) => Promise<void>;
}

/**
 * Create migrations tracking table if it doesn't exist
 */
async function ensureMigrationsTable(client: VercelPoolClient): Promise<void> {
 await client.query(`
    CREATE TABLE IF NOT EXISTS _migrations (
      id SERIAL PRIMARY KEY,
      migration_name TEXT UNIQUE NOT NULL,
      applied_at TIMESTAMP DEFAULT NOW()
    )
  `);
}

/**
 * Get list of already-applied migrations
 */
async function getAppliedMigrations(
 client: VercelPoolClient,
): Promise<Set<string>> {
 const result = await client.query<{ migration_name: string }>(
  "SELECT migration_name FROM _migrations ORDER BY id",
 );
 return new Set(result.rows.map((row) => row.migration_name));
}

/**
 * Get list of migration files from src/db/migrations/ directory
 * Only includes files matching pattern: NNNN_*.ts (e.g., 0001_add_users.ts)
 */
function getMigrationFiles(): string[] {
 const migrationsDir = path.join(__dirname, "migrations");

 if (!fs.existsSync(migrationsDir)) {
  console.log("[Migrations] No migrations directory found");
  return [];
 }

 const files = fs
  .readdirSync(migrationsDir)
  .filter((file) => file.match(/^\\d{4}_.*\\.ts$/))
  .sort(); // Numerical sorting by filename

 return files;
}

/**
 * Load and execute a single migration
 */
async function runMigration(
 client: VercelPoolClient,
 filename: string,
): Promise<void> {
 const migrationPath = path.join(__dirname, "migrations", filename);
 const migrationName = filename.replace(/\\.ts$/, "");

 console.log(`[Migrations] Running: ${migrationName}`);

 // Dynamically import the migration file
 const migrationModule = await import(migrationPath);
 const migration: Migration = migrationModule.migration;

 if (!migration || typeof migration.up !== "function") {
  throw new Error(
   `Invalid migration file: ${filename} (must export migration.up function)`,
  );
 }

 // Run the migration
 await migration.up(client);

 // Record successful migration
 await client.query(
  "INSERT INTO _migrations (migration_name) VALUES ($1) ON CONFLICT (migration_name) DO NOTHING",
  [migrationName],
 );

 console.log(`[Migrations] ✅ Completed: ${migrationName}`);
}

/**
 * Main migration runner
 * Runs all pending migrations in numerical order
 */
export async function runPendingMigrations(
 client: VercelPoolClient,
): Promise<void> {
 try {
  console.log("[Migrations] Checking for pending migrations...");

  // Ensure tracking table exists
  await ensureMigrationsTable(client);

  // Get applied and available migrations
  const appliedMigrations = await getAppliedMigrations(client);
  const migrationFiles = getMigrationFiles();

  // Filter to pending migrations
  const pendingMigrations = migrationFiles.filter(
   (file) => !appliedMigrations.has(file.replace(/\\.ts$/, "")),
  );

  if (pendingMigrations.length === 0) {
   console.log("[Migrations] ✓ All migrations up to date");
   return;
  }

  console.log(
   `[Migrations] Found ${pendingMigrations.length} pending migration(s)`,
  );

  // Run each pending migration
  for (const migrationFile of pendingMigrations) {
   await runMigration(client, migrationFile);
  }

  console.log(
   `[Migrations] ✅ Successfully applied ${pendingMigrations.length} migration(s)\\n`,
  );
 } catch (error) {
  console.error("[Migrations] ❌ Migration failed:", error);
  throw error; // Propagate error to stop deployment if migration fails
 }
}
```

**Key features:**
- Creates `_migrations` tracking table automatically
- Scans `src/db/migrations/` for numbered `.ts` files
- Runs only unapplied migrations
- Records successful migrations in database
- Throws on error (stops deployment if migration fails)

### Task 2: Update init.ts to Call Migration Runner

- [ ] **Integrate migration runner into init.ts**: Navigate to `{{AGENT_PATH}}/src/db/init.ts` and add the migration runner call after schema creation but before auto-bootstrap.

Find this section (around line 98):
```typescript
console.log("[Init] ✅ Schema initialization complete\\n");
```

After that line and before the `finally` block (around line 103), add:
```typescript
 // Run pending database migrations
 try {
  const { runPendingMigrations } = await import("./migration-runner");
  await runPendingMigrations(client);
 } catch (error) {
  console.error("[Init] ❌ Migration runner failed:", error);
  throw error; // Stop init if migrations fail
 }
```

**This ensures migrations run on every deployment, automatically.**

### Task 3: Create migrations Directory

- [ ] **Create migrations folder**: Ensure the `{{AGENT_PATH}}/src/db/migrations/` directory exists:

```bash
mkdir -p {{AGENT_PATH}}/src/db/migrations
```

This directory will hold all numbered migration files (0001, 0002, etc.).

### Task 4: Verify TypeScript Compiles

- [ ] **Check compilation**: Run `npx tsc --noEmit` or check your IDE to ensure the new `migration-runner.ts` file compiles without errors.

- [ ] **Verify imports**: Ensure the import of `@vercel/postgres` types works correctly. The `VercelPoolClient` type should be available from your existing dependencies.

### Task 5: Test Migration Infrastructure (Dry Run)

- [ ] **Verify init.ts integration**: Review the updated `init.ts` to confirm:
  - Migration runner is called after schema creation
  - Migration runner is called before auto-bootstrap
  - Errors are caught and propagated (stops deployment)
  - Console logs are clear and informative

## Success Criteria

- ✅ `src/db/migration-runner.ts` created with complete implementation
- ✅ `src/db/init.ts` updated to call `runPendingMigrations()`
- ✅ `src/db/migrations/` directory exists
- ✅ TypeScript compiles without errors
- ✅ Migration runner integrates into deployment flow
- ✅ Console logs are clear and professional

## Migration Flow

The deployment flow now looks like this:

```
1. npm run build
2. init.ts runs
3. Create schema tables (persons, events, etc.)
4. Create indexes
5. → Run migration runner (NEW!)
6.    → Check for pending migrations
7.    → Run 0001, 0002, etc. in order
8.    → Record in _migrations table
9. Auto-bootstrap legacy data (if empty)
10. App starts
```

## Notes

**Why integrate into init.ts?**
- Runs on every deployment automatically
- No manual commands needed
- Consistent with existing patterns (schema init, auto-bootstrap)
- Stops deployment if migration fails (safe)

**Migration numbering:**
- Use 4 digits: 0001, 0002, 0003, etc.
- Allows up to 9999 migrations (plenty!)
- Lexicographic sort works correctly
- Matches Django/Rails conventions

**Error handling:**
- Migration errors stop deployment (throw error)
- Schema errors are logged but don't stop app (existing pattern)
- This ensures data integrity is maintained

**Future migrations:**
- Just add `0002_your_feature.ts` to migrations folder
- Deploy
- Runs automatically

## Testing Checklist

After implementation:
- [ ] TypeScript compiles
- [ ] init.ts has migration runner call
- [ ] migrations/ directory exists
- [ ] Migration runner handles empty directory gracefully
- [ ] _migrations table creation query is valid SQL

## Status

Mark this document complete when all tasks are checked and verified.

---

**Next**: Document 2 will create the actual migration file (0001_add_activity_tracking.ts) and update the schema.
