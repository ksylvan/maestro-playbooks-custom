# AFG Activity Tracking Playbook

A Maestro Auto Run playbook to add comprehensive user activity tracking to the AFG website with automatic migration system.

## What This Playbook Does

Adds `last_login` and `last_activity` timestamp tracking to the AFG website's Person database, with UI displays across multiple pages. **Includes a Django/Rails-style auto-migration system** that runs automatically on every deployment.

### Features Added

1. **Auto-Migration Infrastructure** (NEW!)
   - Migration runner that tracks applied migrations in database
   - Numbered migrations (0001, 0002, etc.) run automatically
   - Integrated into `init.ts` → runs on every deployment
   - Zero-downtime, idempotent, safe to run multiple times

2. **Database Fields**: Two new timestamp columns in the persons table
   - `last_login` - Updated on magic link authentication
   - `last_activity` - Updated on authenticated page loads

3. **Backend Tracking**:
   - SignIn event handler updates last_login
   - Activity tracking via layout component (server-side, no middleware/proxy needed)

4. **UI Displays**:
   - Profile page shows last login timestamp
   - Home page shows "You were last here on..." if >24 hours inactive
   - New Admin User Activity page with full activity table
   - Admin dashboard card linking to activity tracking

## Migration System

This playbook creates an industry-standard migration system following Django/Rails patterns:

**How It Works:**
```
Deployment → init.ts → Migration Runner → Auto-Bootstrap → App Starts
                ↓           ↓                   ↓
            Schema     Run Pending         Import Legacy
            Tables     Migrations           (if empty)
```

**Key Files:**
- `src/db/migration-runner.ts` - Orchestrates migration execution
- `src/db/migrations/0001_add_activity_tracking.ts` - First numbered migration
- `_migrations` table - Tracks which migrations have been applied

**Benefits:**
- ✅ No manual migration commands needed
- ✅ Works for both fresh installs and updates
- ✅ Scalable: easily add 0002, 0003, etc.
- ✅ Safe: tracks state, runs only once
- ✅ Industry standard pattern

## Playbook Structure

This playbook follows a **linear 6-document pattern** (no looping):

```
1_MIGRATION_INFRASTRUCTURE.md - Create migration runner system
2_UPDATE_DATABASE.md          - Schema, migration file, types
3_ADD_QUERIES.md              - Database query functions
4_TRACK_ACTIVITY.md           - Auth event handler and middleware
5_ADMIN_UI.md                 - Admin activity page and dashboard card
6_USER_UI.md                  - Profile and home page UI updates
```

## Prerequisites

- Maestro installed and configured
- AFG website project accessible to the Maestro agent
- Agent with access to the AFG codebase
- PostgreSQL database (Neon/Vercel)

## Setup in Maestro

1. Place this folder in your Maestro Auto Run directory
2. Select an agent with access to the AFG repo
3. Configure Auto Run settings:
   - **Loop Mode**: OFF (sequential execution)
   - **Max Loops**: N/A (no looping)
   - **Agent Path**: use the worktree or repo location for afg-app

4. Run the playbook

## Execution Flow

The playbook executes documents sequentially:

1. **Document 1** → Creates migration infrastructure (runner + tracking)
2. **Document 2** → Updates database schema, creates migration, updates types
3. **Document 3** → Adds query functions to db-queries.ts
4. **Document 4** → Implements tracking in auth.ts and layout component
5. **Document 5** → Creates admin UI (activity page + dashboard card)
6. **Document 6** → Updates user-facing UI (profile + home page)

## What Gets Modified

### Existing Files Modified

- `src/db/init.ts` - Add migration runner call
- `src/db/schema.ts` - Add columns and indexes
- `src/lib/types.ts` - Add optional timestamp fields
- `src/lib/auth.ts` - Update signIn event handler
- `src/lib/db-queries.ts` - Add getUserActivityRecords() function
- `src/app/page.tsx` - Add conditional activity message
- `src/app/profile/page.tsx` - Display last_login
- `src/app/admin/page.tsx` - Add User Activity card

### New Files Created

- `src/db/migration-runner.ts` - **NEW: Migration orchestration system**
- `src/db/migrations/0001_add_activity_tracking.ts` - **NEW: Numbered migration**
- `src/lib/track-activity.ts` - Server-side activity tracking helper
- `src/app/admin/activity/page.tsx` - Admin activity panel

## Verification

After completion, verify:

- [ ] TypeScript compiles without errors
- [ ] Migration system works (check `_migrations` table)
- [ ] Login updates last_login timestamp
- [ ] Page visits update last_activity
- [ ] Profile page shows last login
- [ ] Home page shows conditional message
- [ ] Admin can view User Activity page
- [ ] All timestamps display correctly

## Deployment

The playbook creates a **fully automated deployment flow**:

1. **Build**: `npm run build` calls `init.ts`
2. **Init**: Creates schema tables (if needed)
3. **Migrate**: **Automatically runs pending migrations**
4. **Bootstrap**: Imports legacy data (if database is empty)
5. **Start**: App starts with all features working

**No manual commands needed!** Just deploy and the migrations run automatically.

## Future Migrations

To add more migrations in the future:

1. Create `src/db/migrations/0002_your_migration.ts`
2. Follow the same format as 0001
3. Deploy
4. Migration runs automatically

## Architecture Notes

- **Performance**: Middleware uses indexed queries (<50ms overhead)
- **Safety**: Non-blocking error handling prevents login failures
- **Compatibility**: NULL-safe, backward compatible with existing records
- **Security**: Parameterized SQL queries, proper pool management
- **Migrations**: Track state in `_migrations` table, run in numerical order

## Exit Conditions

Playbook completes when all 6 documents report `COMPLETED` status.

## Support

For issues or questions, refer to the AFG website codebase documentation or Maestro documentation at <https://github.com/pedramamini/Maestro>.
