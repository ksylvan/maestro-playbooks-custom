# Document 4: Implement Activity Tracking

## Context

- **Agent**: {{AGENT_NAME}}
- **Project**: {{AGENT_PATH}}
- **Date**: {{DATE}}

## Purpose

Implement tracking for `last_login` (auth event) and `last_activity` (layout component).

**IMPORTANT**: Next.js 16 deprecated `middleware.ts` in favor of `proxy.ts`, but the framework discourages heavy database operations in proxy. Instead, we use a **server-side layout component** to track activity - this is the recommended Next.js 16 pattern.

## Tasks

### Task 1: Update signIn Event (may already be done!)

- [ ] **Check if already implemented**: Navigate to `{{AGENT_PATH}}/src/lib/auth.ts` lines 167-185 and check if the signIn event already updates last_login. If it does, mark this task complete and move on.

- [ ] **If not implemented, update signIn event** in `{{AGENT_PATH}}/src/lib/auth.ts` (lines 167-171):

```typescript
events: {
  async signIn({ user }) {
    console.log(`✅ User signed in: ${user.email}`);

    // Update last_login timestamp in persons table
    if (user.email) {
      try {
        await pool.query(
          'UPDATE persons SET last_login = NOW() WHERE email = $1',
          [user.email]
        );
        console.log(`🕐 Updated last_login for ${user.email}`);
      } catch (error) {
        console.error('❌ Failed to update last_login:', error);
        // Don't throw - login should still succeed
      }
    }
  },
},
```

**Note**: Uses existing `pool` variable from auth.ts closure (line 14). Non-blocking error handling.

### Task 2: Create Activity Tracking Helper

- [ ] **Create tracking helper**: Create `{{AGENT_PATH}}/src/lib/track-activity.ts`:

```typescript
import { Pool } from "@neondatabase/serverless";

/**
 * Update last_activity timestamp for authenticated user
 * Called from layout components on page load
 * Non-blocking, fire-and-forget pattern
 */
export async function trackActivity(userEmail: string): Promise<void> {
  // Fire and forget - don't await
  Promise.resolve().then(async () => {
    const pool = new Pool({ connectionString: process.env.DATABASE_URL });
    try {
      await pool.query(
        'UPDATE persons SET last_activity = NOW() WHERE email = $1',
        [userEmail]
      );
    } catch (error) {
      // Silent fail - activity tracking is non-critical
      console.error('Failed to track activity:', error);
    } finally {
      await pool.end();
    }
  });
}
```

**Why this approach?**
- No middleware/proxy file needed (avoids Next.js 16 deprecation)
- Runs server-side in layout (after auth check)
- Fire-and-forget pattern doesn't slow page loads
- Follows Next.js 16 recommended patterns

### Task 3: Update Root Layout to Track Activity

- [ ] **Add activity tracking to layout**: Navigate to `{{AGENT_PATH}}/src/app/layout.tsx` and add tracking at the top of the component:

Find the export default function RootLayout and add this code **at the very beginning** of the function body:

```typescript
export default async function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  // Track activity for authenticated users (fire and forget)
  const session = await auth();
  if (session?.user?.email) {
    const { trackActivity } = await import('@/lib/track-activity');
    trackActivity(session.user.email); // Don't await - fire and forget
  }

  // ... rest of layout code
```

**This tracks activity on every authenticated page load without blocking rendering.**

### Task 4: Verify No Middleware/Proxy Files Exist

- [ ] **Clean up any middleware files**: Check for and remove these files if they exist:
  - `{{AGENT_PATH}}/src/middleware.ts`
  - `{{AGENT_PATH}}/src/proxy.ts`
  - `{{AGENT_PATH}}/middleware.ts`
  - `{{AGENT_PATH}}/proxy.ts`

**Why?** We're using the layout approach instead, which is more compatible with Next.js 16 and doesn't cause build errors.

### Task 5: Test Activity Tracking

- [ ] **Test login tracking**:
  1. Sign in with magic link
  2. Check database - `last_login` should update
  3. Check console logs for "🕐 Updated last_login" message

- [ ] **Test activity tracking**:
  1. Navigate to different pages while logged in
  2. Check database - `last_activity` should update on page loads
  3. Verify page load speed isn't impacted (fire-and-forget pattern)

### Task 6: Verify TypeScript Compiles

- [ ] **Check compilation**: Run `npx tsc --noEmit` to ensure no errors

## Success Criteria

- ✅ signIn event updates last_login (may already be done)
- ✅ track-activity.ts helper created
- ✅ Root layout calls trackActivity for authenticated users
- ✅ No middleware.ts or proxy.ts files exist
- ✅ Login updates last_login in database
- ✅ Page navigation updates last_activity in database
- ✅ TypeScript compiles without errors
- ✅ No build errors about middleware/proxy conflict

## Why This Approach?

**Next.js 16 Changes:**
- `middleware.ts` → deprecated
- `proxy.ts` → meant for lightweight routing/redirects only
- Database operations → should be in Server Components/Actions

**Our Solution:**
- Use root layout (server component) for activity tracking
- Fire-and-forget pattern (doesn't block rendering)
- No middleware/proxy conflicts
- Follows Next.js 16 best practices

## Performance Notes

- **Fire-and-forget**: Activity update doesn't block page rendering
- **Server-side**: Runs on every page load, but doesn't slow down client
- **Non-critical**: Silent failures don't impact user experience
- **Indexed queries**: Fast UPDATE with indexed email column

## Status

Mark complete when all tasks checked and both login + activity tracking work correctly.

---

**Next**: Document 5 will create the admin UI for viewing activity data.
