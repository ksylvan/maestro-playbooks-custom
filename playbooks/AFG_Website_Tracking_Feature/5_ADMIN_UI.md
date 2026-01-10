# Document 5: Create Admin UI Components

## Context

- **Agent**: {{AGENT_NAME}}
- **Project**: {{AGENT_PATH}}

## Purpose

Create admin activity tracking page and dashboard card.

## Tasks

- [ ] Create `{{AGENT_PATH}}/src/app/admin/activity/page.tsx` with full activity table (see original playbook doc 4 for complete implementation)

- [ ] Add dashboard card to `{{AGENT_PATH}}/src/app/admin/page.tsx` after line 149:
```tsx
<Link href="/admin/activity" className="group rounded-lg bg-white dark:bg-gray-900 p-6 shadow-lg transition-all hover:shadow-xl border-2 border-transparent hover:border-[--afg-amber-500]">
  {/* Activity icon and content - see original doc 4 for complete code */}
  <h3>User Activity</h3>
  <p>View login and activity history for all registered members.</p>
</Link>
```

- [ ] Verify admin can access /admin/activity
- [ ] Verify table displays correctly

## Status

Mark complete when admin UI works.
