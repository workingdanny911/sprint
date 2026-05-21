# Work Board

> Real-time view of current work. Full backlog in BACKLOG.md.

---

## Before You Start (REQUIRED)

- [ ] Read `BACKLOG.md` - Find your assigned or available tasks
- [ ] Read `refs/decisions/F-{slug}-*.md` - If working on a Feature (if exists)
- [ ] Read `refs/lessons/F-{slug}-*.md` - If working on a Feature (if exists)
- [ ] Check `active/F-{slug}.md` - Feature context (if exists)
- [ ] Check **Dependencies** below - Ensure nothing blocks your task

---

## WIP Limit: 1 task per agent

Each agent may only have **one task in_progress** at a time.
Complete or move to review before claiming another.

---

## In Progress

| Feature | Task | Agent | Started | Notes |
|---------|------|-------|---------|-------|
| F-user-auth | T-token-refresh: Token Refresh API | #danny | 2024-01-28 | JWT refresh impl |

---

## In Review

| Feature | Task | Agent | Reviewer | Notes |
|---------|------|-------|----------|-------|
| | | | | |

---

## Recently Done

> Last 5 items. Archive older to refs/changelog.md.

| Feature | Task | Agent | Completed | Notes |
|---------|------|-------|-----------|-------|
| F-user-auth | T-signup-api: Signup API | #danny | 2024-01-28 | With email validation |
| F-user-auth | T-login-api: Login API | #danny | 2024-01-27 | JWT issuance |

---

## Dependencies

> Feature/Task dependencies. Check before starting work.

| Item | Depends On | Status | Notes |
|------|-----------|--------|-------|
| F-user-profile | F-user-auth | F-user-auth in_progress | F-user-profile starts after F-user-auth complete |

---

## Blockers

| Task | Agent | Blocker | Since |
|------|-------|---------|-------|
| | | | |

---

## Quick Actions

### Claim a task
1. Find unclaimed task in BACKLOG.md (no #agent)
2. Add `#yourname` to task in BACKLOG.md, change status to `in_progress`
3. Add entry to **In Progress** table above
4. Create `active/F-{slug}.md` if first task for that feature

### Complete a task
1. Move from **In Progress** to **Recently Done** (or **In Review**)
2. Update BACKLOG.md: mark with `[x]`, status to `done` or `review`
3. Update `active/F-{slug}.md` with completion notes
4. **END SESSION** - Do NOT claim next task automatically

> One session = One task. Start new session with `@INSTRUCTION.md` for next task.

### Handle blocker
1. Add to **Blockers** table above
2. Document in your In Progress notes
3. Do NOT claim new task (respect WIP limit)
4. Notify team lead if critical
