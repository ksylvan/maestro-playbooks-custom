# Document 2: Update Database Schema

## Context

- **Playbook**: AFG Activity Tracking with Auto-Migration System
- **Agent**: {{AGENT_NAME}}
- **Project**: {{AGENT_PATH}}
- **Date**: {{DATE}}
- **Working Folder**: {{AUTORUN_FOLDER}}

## Purpose

Update the database schema to support activity tracking by creating a numbered migration file, updating the base schema for fresh installs, and updating TypeScript types.

## Tasks

### Task 1: Create Numbered Migration File

- [ ] **Create 0001_add_activity_tracking.ts**: Create file at `{{AGENT_PATH}}/src/db/migrations/0001_add_activity_tracking.ts`:

```typescript
import type { VercelPoolClient } from "@vercel/postgres";

/**
 * Migration 0001: Add activity tracking to persons table
 *
 * Adds two timestamp columns:
 * - last_login: Set when user authenticates via magic link
 * - last_activity: Set on any authenticated page visit
 */
export const migration = {
  name: "0001_add_activity_tracking",

  async up(client: VercelPoolClient) {
    console.log("[0001] Adding activity tracking columns...");

    // Add columns (idempotent with IF NOT EXISTS)
    await client.query(`
      ALTER TABLE persons
      ADD COLUMN IF NOT EXISTS last_login TIMESTAMP,
      ADD COLUMN IF NOT EXISTS last_activity TIMESTAMP
    `);

    // Add indexes for performance
    await client.query(`
      CREATE INDEX IF NOT EXISTS idx_persons_last_login
      ON persons(last_login)
    `);

    await client.query(`
      CREATE INDEX IF NOT EXISTS idx_persons_last_activity
      ON persons(last_activity)
    `);

    console.log("[0001] ✅ Activity tracking columns and indexes added");
  },

  async down(client: VercelPoolClient) {
    // Rollback (optional, for reference)
    await client.query(`
      DROP INDEX IF EXISTS idx_persons_last_activity;
      DROP INDEX IF EXISTS idx_persons_last_login;
      ALTER TABLE persons DROP COLUMN IF EXISTS last_activity;
      ALTER TABLE persons DROP COLUMN IF EXISTS last_login;
    `);
  }
};
```

### Task 2: Update Base Schema (for fresh installs)

- [ ] **Update schema.ts**: Navigate to `{{AGENT_PATH}}/src/db/schema.ts` and add columns to `createPersonsTableSQL`.

Find the line with `created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP` (around line 21) and add after it:
```sql
  last_login TIMESTAMP,
  last_activity TIMESTAMP
```

Then update the `createIndexesSQL` array to include:
```sql
"CREATE INDEX IF NOT EXISTS idx_persons_last_login ON persons(last_login)",
"CREATE INDEX IF NOT EXISTS idx_persons_last_activity ON persons(last_activity)"
```

**This ensures fresh database installs include the columns from the start.**

### Task 3: Update TypeScript Types

- [ ] **Update Person interface**: Navigate to `{{AGENT_PATH}}/src/lib/types.ts` and add to the `Person` interface after `created_at`:
```typescript
  last_login?: Date | null;
  last_activity?: Date | null;
```

### Task 4: Verify Changes

- [ ] **TypeScript compilation**: Run `npx tsc --noEmit` to ensure no type errors
- [ ] **Migration format**: Verify migration file exports `migration` object with `name` and `up` function
- [ ] **SQL syntax**: Review SQL in migration file for correctness

## Success Criteria

- ✅ Migration file created at `src/db/migrations/0001_add_activity_tracking.ts`
- ✅ Migration exports correct format
- ✅ Base schema updated with new columns
- ✅ Base schema updated with new indexes
- ✅ TypeScript types updated
- ✅ No compilation errors
- ✅ SQL is idempotent (IF NOT EXISTS clauses)

## Notes

- Migration file runs on existing databases
- Schema.ts changes apply to fresh installs
- TypeScript types are optional to handle NULL values
- Both paths covered: migration (existing) + schema (fresh)

## Status

Mark complete when all tasks checked and TypeScript compiles.
