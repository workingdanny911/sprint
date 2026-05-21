# Backlog

> Single source of truth for all work items.
> Use `/sprint:add-backlog` to add new items.

---

## Legend

**Priority**: Top-to-bottom ordering. Items at top are highest priority.

**Urgent**: Prefix with `[URGENT]` to indicate time-sensitive items.

**Assignment**: `#agent-name` indicates who is working on it.

---

## Identifiers

```
F-{feature-slug}        - Feature (F-user-auth, F-user-profile...)
T-{task-slug}           - Task: content-based, globally unique (T-login-api...)
T-{task-slug}.{sub-slug} - Sub-task (T-login-api.schema...)
```

Example: `T-login-api.schema` = the schema sub-task of the Login API task

---

## Status & Transitions

```
backlog ──────► in_progress ──────► review ──────► done
                    │                  │
                    ▼                  │
                 blocked ◄─────────────┘
                    │
                    ▼
                 backlog (when unblocked)
```

| Status | Meaning |
|--------|---------|
| `backlog` | Not started, available to claim |
| `in_progress` | Being worked on (see HANDOFF.md) |
| `blocked` | Cannot proceed, waiting on dependency |
| `review` | Work complete, awaiting review |
| `done` | Reviewed and accepted |

---

## Features

### F-user-auth: User Authentication
> User authentication API (login, signup, token refresh)

**Design**: [refs/designs/F-user-auth.md](refs/designs/F-user-auth.md)
**Context**: [active/F-user-auth.md](active/F-user-auth.md)

- [x] T-login-api: Login API `done`
  - [x] T-login-api.schema: Define request/response schema
  - [x] T-login-api.endpoint: Implement endpoint
  - [x] T-login-api.validation: Add validation
- [x] T-signup-api: Signup API `done`
- [ ] T-token-refresh: Token Refresh API #danny `in_progress`
- [ ] T-review-user-auth: Review & Refactor `backlog`

---

### F-user-profile: User Profile
> User profile management (view, update)

**Design**: [refs/designs/F-user-profile.md](refs/designs/F-user-profile.md)

- [ ] T-get-profile: Get Profile API `backlog`
- [ ] T-update-profile: Update Profile API `backlog`
- [ ] T-review-user-profile: Review & Refactor `backlog`

---

## Done

> Completed features. Keep last 3 summaries here.

*None yet.*

---

## Archiving

When a Feature is `done`:

1. Move full Feature section → `refs/archive/F-{slug}.md`
2. Leave one-line summary here:
   - `F-{slug}: Feature Name` - completed YYYY-MM-DD
