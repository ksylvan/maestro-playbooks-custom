# Document 6: Update User-Facing UI

## Context

- **Agent**: {{AGENT_NAME}}
- **Project**: {{AGENT_PATH}}

## Purpose

Add last_login to profile page and conditional activity message to home page.

## Tasks

- [ ] Update `{{AGENT_PATH}}/src/app/profile/page.tsx` after line 26:
```tsx
{person.last_login && (
  <div className="mb-6 rounded-lg bg-blue-50 dark:bg-blue-950 border-2 border-blue-200 dark:border-blue-800 p-4">
    <p className="text-sm text-gray-700 dark:text-gray-300">
      <span className="font-semibold">Last signed in:</span>{' '}
      {new Date(person.last_login).toLocaleString('en-US', {
        dateStyle: 'full',
        timeStyle: 'short',
      })}
    </p>
  </div>
)}
```

- [ ] Update `{{AGENT_PATH}}/src/app/page.tsx` - fetch person data and add conditional message (see original doc 5 for complete implementation)

- [ ] Test profile shows last login
- [ ] Test home shows message when >24 hours inactive

## Status

Mark complete when all UI updates work correctly.

---

## Playbook Complete

🎉 If all 6 documents complete, activity tracking is fully implemented with auto-migration system!

**What was added:**
- ✅ Auto-migration infrastructure (Django/Rails style)
- ✅ Database schema with activity columns
- ✅ Tracking via auth + middleware
- ✅ Admin activity dashboard
- ✅ User-facing UI updates

**Deploy and it just works!** No manual migration commands needed.
