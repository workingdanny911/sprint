# Backlog

> Single source of truth for all work items.
> Use `/sprint:add-backlog` to add new items.

---

## Legend

**Priority**: Top-to-bottom ordering. Items at top are highest priority.

**Urgent**: Prefix with `[URGENT]` to indicate time-sensitive items.

**Assignment**: `#persona-name` indicates who is working on it (e.g., `#rook`, `#pixel`, `#dash`).

---

## Identifiers

```
F-{feature-slug}          - Feature, identified by a kebab-case slug (F-user-auth, F-payment)
T-{task-slug}             - Task, an independent kebab-case slug (no Feature prefix): T-login-api, T-checkout-ui
T-{task-slug}.{sub-slug}  - Sub-task, one level: T-login-api.schema, T-login-api.handler
```

- **Feature slug**: kebab-case of the Feature name. Must be unique within the sprint.
- **Task slug**: kebab-case derived from the Task's content, **globally unique across the sprint** (no Feature prefix). No sequence numbers.
- **Sub-task**: append `.{sub-slug}` (one level only).

Example: `T-login-api.schema` = the `schema` sub-task of Task `T-login-api`.

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

*No features yet. Use `/sprint:add-backlog` to add work items.*

<!-- Example structure:

### [URGENT] F-user-auth: User Authentication
> Brief description

**Design**: [refs/designs/F-user-auth.md](refs/designs/F-user-auth.md)
**Context**: [active/F-user-auth.md](active/F-user-auth.md) (when in progress)

- [ ] T-login-api: Task name `backlog`
  - [ ] T-login-api.schema: Sub-task
  - [ ] T-login-api.handler: Sub-task
- [ ] T-session-store: Task name #rook `in_progress`
- [x] T-auth-middleware: Task name `done`

---

### F-payment: Payment System
> Description

**Design**: [refs/designs/F-payment.md](refs/designs/F-payment.md)

- [ ] T-checkout-flow: Task name `backlog`

-->

<!-- Worktree mode example:

### F-payment: Payment System
> Brief description

**Design**: [{sprintRoot}/refs/designs/F-payment.md]({sprintRoot}/refs/designs/F-payment.md)
**Context**: [{sprintRoot}/active/F-payment.md]({sprintRoot}/active/F-payment.md)
**Branch**: feature/F-payment
**Worktree**: /path/to/worktrees/F-payment

- [ ] T-checkout-flow: Task name `backlog`
- [ ] T-webhook-handler: Task name `backlog`
- [ ] T-review-payment: Review & Refactor `backlog`
- [ ] T-merge-payment: Merge feature/F-payment and clean up worktree `backlog`

-->

---

## Done

> Completed features. Keep last 3 summaries here.

*None yet.*

<!-- When a Feature is `done`:
1. Remove the Feature section from above
2. Add one-line summary here: `F-user-auth: User Authentication - completed YYYY-MM-DD`
3. Archive active context: move `active/F-{slug}.md` → `refs/archive/F-{slug}.md`
   (See INSTRUCTION.md "Feature Context Management" for full steps)
-->
