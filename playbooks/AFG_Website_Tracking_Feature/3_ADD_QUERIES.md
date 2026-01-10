# Document 3: Add Database Query Functions

## Context

- **Playbook**: AFG Activity Tracking
- **Agent**: {{AGENT_NAME}}
- **Project**: {{AGENT_PATH}}

## Purpose

Add query functions to support fetching user activity data for admin dashboard.

## Tasks

- [ ] Add `UserActivityRecord` interface to `{{AGENT_PATH}}/src/lib/db-queries.ts`:
```typescript
export interface UserActivityRecord {
  id: number;
  email: string;
  first_name: string;
  last_name: string;
  first_sign_in: Date | null;
  last_login: Date | null;
  last_activity: Date | null;
  is_admin: boolean;
  is_inactive: boolean;
}
```

- [ ] Add `getUserActivityRecords()` function to same file:
```typescript
export async function getUserActivityRecords(): Promise<UserActivityRecord[]> {
  const pool = new Pool({ connectionString: process.env.DATABASE_URL });
  const result = await pool.query<UserActivityRecord>(`
    SELECT
      p.id, p.email, p.first_name, p.last_name,
      u."emailVerified" as first_sign_in,
      p.last_login, p.last_activity,
      p.is_admin, p.is_inactive
    FROM persons p
    LEFT JOIN users u ON p.email = u.email
    WHERE u."emailVerified" IS NOT NULL
    ORDER BY p.last_activity DESC NULLS LAST, p.last_login DESC NULLS LAST
  `);
  await pool.end();
  return result.rows;
}
```

- [ ] Verify TypeScript compiles

## Status

Mark complete when all tasks done.
