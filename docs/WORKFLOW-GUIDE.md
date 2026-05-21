# Sprint Plugin - Workflow Guide

## Overall Workflow

```
┌────────────────────────────────────────────────────────────────────┐
│                        Sprint Workflow                              │
└────────────────────────────────────────────────────────────────────┘

 Phase 1: Setup              Phase 2: Planning           Phase 3: Execution
 ────────────────           ────────────────            ─────────────────
 /sprint:init               /sprint:add-backlog         @INSTRUCTION.md
       │                          │                           │
       ▼                          ▼                           ▼
 Create Sprint              Define Feature/Task         Work on Task
       │                          │                           │
       │                          ▼                           ▼
       │                   /sprint:plan-backlog        Complete Task
       │                          │                           │
       │                          ▼                           ▼
       │                   Break into Sub-tasks         End Session
       │                                                      │
       └──────────────────────────────────────────────────────┘
                                   │
                                   ▼
                            /sprint:review-backlog
                                   │
                                   ▼
                             Quality Review
                                   │
                                   ▼
                            Add Improvement Tasks (if needed)
```

---

## Phase 1: Sprint Initialization

### 1.1 Run `/sprint:init`

```
User: /sprint:init
```

### 1.2 Provide Information

Claude will ask:

1. **Sprint name**: kebab-case recommended
   ```
   e.g., payment-system, auth-refactor, dashboard-v2
   ```

2. **Location**: Default `sprints/<sprint-name>/`
   ```
   e.g., sprints/payment-system/
   ```

3. **Team (optional)**: Agent names
   ```
   e.g., #rook, #pixel, #danny
   ```

### 1.2a Provide Information (Worktree Mode)

Claude will ask:

1. **Sprint name**: kebab-case recommended
2. **Location**: Default `sprints/<sprint-name>/`
3. **Team** (optional): Agent names
4. **Mode**: `worktree` selected
   - Sprint path added to `.gitignore`
   - `.sprint-config` created with worktree mode

### 1.3 Result

```
sprints/payment-system/
├── BACKLOG.md
├── HANDOFF.md
├── INSTRUCTION.md
├── .sprint-version
├── active/
│   └── README.md
└── refs/
    ├── designs/
    ├── plans/
    ├── decisions/
    │   └── _sprint.md
    ├── lessons/
    │   └── _sprint.md
    └── archive/
```

---

## Phase 2: Work Planning

### 2.1 Add Features

#### Run `/sprint:add-backlog`

```
User: /sprint:add-backlog
```

#### Brainstorming Process

```
Claude: What do you want to build?

User: User authentication feature

Claude: What type of work is this?
1. coding (recommended)
2. docs
3. ideation
4. general

User: 1

Claude: Let's define the scope.

Include:
- Login
- Signup
- Password reset

Anything to exclude?

User: Social login for later

Claude: What are the success criteria?

User: APIs work and tests pass
```

#### Proposed Structure

```markdown
### F-user-auth: User Authentication
> User auth (login, signup, password reset)
> Social login excluded

**Type**: coding

- [ ] T-login-api: Login API `backlog`
- [ ] T-signup-api: Signup API `backlog`
- [ ] T-password-reset: Password Reset API `backlog`
- [ ] T-auth-integration-tests: Auth Integration Tests `backlog`
- [ ] T-review-user-auth: Review & Refactor `backlog`

Proceed?

User: Yes

Claude: Added to BACKLOG.md.

Create a design document? (2+ tasks → recommended)

User: Yes

Claude: Created refs/designs/F-user-auth.md
```

### 2.1a Add Features (Worktree Mode)

Additional steps when adding features in worktree mode:

1. Worktree selection: Create new or use existing
2. Branch/worktree path recorded in BACKLOG.md
3. Auto-added: Merge worktree Task as final Task

### 2.2 Detailed Task Planning (Optional)

For complex Tasks, use `/sprint:plan-backlog`:

```
User: /sprint:plan-backlog T-login-api
```

#### Brainstorming → Planning

```
Claude: Let's design T-login-api: Login API.

Which authentication method?
1. JWT (recommended) - stateless, scalable
2. Session - server-based
3. OAuth only - external auth

User: 1

Claude: Let me verify my understanding.

## Overview
Login API authenticates with email/password and issues JWT tokens.
- Access Token: 15 min validity
- Refresh Token: 7 day validity

Correct so far?

User: Yes

[Enter Plan Mode]

Claude: I propose the following plan:

- [ ] T-login-api.schema: Define request/response schema
- [ ] T-login-api.endpoint: Implement login endpoint
- [ ] T-login-api.validation: Add input validation
- [ ] T-login-api.rate-limiting: Add rate limiting
```

---

## Phase 3: Execution

### 3.1 Start Session

```
User: @sprints/payment-system/INSTRUCTION.md #rook
```

### 3.1a Start Session (Worktree Mode)

```
User: @sprints/my-sprint/INSTRUCTION.md #rook

Claude: Starting session as #rook.

[Reading sprint files from Sprint Root: /abs/path/sprints/my-sprint/]
[Code work in worktree: /abs/path/worktrees/F-user-auth/]
```

### 3.2 Agent Behavior

```
Claude: Starting session as #rook.

[Reading BACKLOG.md, HANDOFF.md]

Current state:
- F-user-auth: User Authentication (4 tasks)
  - T-login-api: Login API `backlog` ← Top priority
  - T-signup-api: Signup API `backlog`
  - ...

Claiming and starting T-login-api: Login API.
```

### 3.3 Working

What Claude does:

1. **Update BACKLOG.md**
   ```markdown
   - [ ] T-login-api: Login API #rook `in_progress`
   ```

2. **Update HANDOFF.md**
   ```markdown
   | F-user-auth | T-login-api: Login API | #rook | 2024-01-28 | JWT impl |
   ```

3. **Create active/ file** (if first Task for Feature)
   ```
   active/F-user-auth.md
   ```

4. **Execute actual work**

5. **Record progress**

### 3.4 Task Completion

```
Claude: Completed T-login-api: Login API.

Updates:
- BACKLOG.md: T-login-api → review
- HANDOFF.md: Moved to In Review
- active/F-user-auth.md: Added completion notes

Summary of work done:
- Implemented JWT-based login endpoint
- Added input validation and error handling
- All tests passing

Please review when ready. Ending session.
```

**Important**: Agent marks task as `review`, NOT `done`. The task becomes `done` only after user review.

### 3.5 User Review

User reviews the `review` task in the next session:

```
User: @sprints/payment-system/INSTRUCTION.md #rook

Claude: Starting session as #rook.

Found T-login-api: Login API in `review` status.

Here's what was done:
- Implemented POST /auth/login endpoint
- JWT access token (15min) + refresh token (7day)
- Input validation with rate limiting
- Tests: 12 passing

Please review. Approve to mark done, or let me know what needs changes.
```

| User Decision | What Happens |
|---------------|--------------|
| Approve | T-login-api → `done`, moved to Recently Done |
| Request changes | Improvement tasks created or fixes applied, then re-mark `review` |

### 3.6 Next Task

Start new session:

```
User: @sprints/payment-system/INSTRUCTION.md #rook
```

---

## Phase 3.5: Automated Feature Execution (Alternative)

Instead of manually starting sessions for each Task, use `/sprint:work-on-feature` to automate the entire Feature with an **Agent Team**.

### Run

```
User: /sprint:work-on-feature F-user-auth
```

### What Happens

```
Claude: This Feature has 5 Tasks:
- T-login-api: Login API
- T-signup-api: Signup API
- T-password-reset: Password Reset API
- T-auth-integration-tests: Auth Integration Tests
- T-review-user-auth: Review & Refactor

Would you like a detailed explanation first?

User: No

Claude: ## Execution Plan for F-user-auth: User Authentication

**Team**: feature-F-user-auth
**Model**: opus (all teammates)

**Batch 1** (parallel)
| Task | Teammate | Persona | Type | Plan |
|------|----------|---------|------|------|
| T-login-api: Login API | worker-T-login-api | rook | coding | refs/plans/F-user-auth-T-login-api.md |
| T-signup-api: Signup API | worker-T-signup-api | rook | coding | none |

**Batch 2** (after Batch 1)
| Task | Teammate | Persona | Type | Plan |
|------|----------|---------|------|------|
| T-password-reset: Password Reset API | worker-T-password-reset | rook | coding | none |
| T-auth-integration-tests: Auth Integration Tests | worker-T-auth-integration-tests | rook | coding | none |

**Verifier**: verifier (thorn, spawned per verification cycle)
**Review & Refactor**: reviewer-F-user-auth (thorn)

Proceed?

User: Yes

Claude: [Creates Agent Team "feature-F-user-auth"]
[Spawns worker-T-login-api, worker-T-signup-api as teammates (opus)]

✓ T-login-api done (rook) — 3 self-review fixes, 0 verification fixes
✓ T-signup-api done (rook) — 1 self-review fix, 1 verification fix
✓ T-password-reset done (rook) — 2 self-review fixes, 0 verification fixes
✓ T-auth-integration-tests done (rook) — 0 self-review fixes, 0 verification fixes

Running Review & Refactor...

## Feature F-user-auth: User Authentication — Execution Complete

**Team**: feature-F-user-auth (shutdown)

| Task | Teammate | Persona | Review Fixes | Verification Fixes | Status |
|------|----------|---------|-------------|-------------------|--------|
| T-login-api | worker-T-login-api | rook | 3 self-review | 0 | ✓ done |
| T-signup-api | worker-T-signup-api | rook | 1 self-review | 1 | ✓ done |
| T-password-reset | worker-T-password-reset | rook | 2 self-review | 0 | ✓ done |
| T-auth-integration-tests | worker-T-auth-integration-tests | rook | 0 | 0 | ✓ done |
| T-review-user-auth R&R | reviewer-F-user-auth | thorn | 3 | — | ⏳ review |

R&R Task is in review status. Please verify and mark done.
```

### Agent Team Communication Flow

```
Lead ──SendMessage──► worker-T-login-api: "implement task"
     ◄──SendMessage── worker-T-login-api: "implementation complete, files: [...]"
Lead ──SendMessage──► worker-T-login-api: "run /sprint:review-work"
     ◄──SendMessage── worker-T-login-api: "findings: 🔴 1, 🟡 1, 🟢 1, 💡 0"
Lead ──SendMessage──► worker-T-login-api: "fix all (including suggestions)"
     ◄──SendMessage── worker-T-login-api: "all fixed, updated files: [...]"
Lead spawns verifier-T-login-api (thorn, opus)
     verifier ◄──SendMessage──► worker-T-login-api  (design intent Q&A)
     ◄──SendMessage── verifier-T-login-api: "PASS"
Lead ──shutdown──► worker-T-login-api, verifier-T-login-api
```

### Resume After Failure

If execution stops due to an error, re-run the same command:

```
User: /sprint:work-on-feature F-user-auth
```

The orchestrator detects completed Tasks and resumes from where it stopped.

---

## Phase 4: Review

### 4.1 Run Review

```
User: /sprint:review-backlog T-login-api
```

### 4.2 Apply Checklist

Review with type-appropriate checklist (`coding`):

```
Claude: Review results for T-login-api: Login API

### 🔴 Critical (1)
1. Password hashing not applied
   - Location: src/auth/login.ts:24
   - Impact: Security vulnerability

### 🟡 Improvement (2)
1. Error message exposure
   - Current: "User not found"
   - Suggestion: Use generic message

2. Rate limiting not applied
   - Suggestion: Prevent brute force

### 🟢 Minor (1)
- Variable naming could improve

Create improvement plan?

User: Yes

[Enter Plan Mode]

Claude: Adding following improvement Tasks:

- [ ] T-fix-login-api: Fix review issues (T-login-api) `backlog`
  - [ ] T-fix-login-api.password-hashing: Add password hashing
  - [ ] T-fix-login-api.error-messages: Generalize error messages
  - [ ] T-fix-login-api.rate-limiting: Add rate limiting
```

---

## Multi-Agent Scenarios

### Concurrent Work

```
┌────────────────┐     ┌────────────────┐
│  Session 1     │     │  Session 2     │
│  #rook         │     │  #pixel        │
│  T-login-api   │     │  T-get-profile │
└───────┬────────┘     └───────┬────────┘
        │                      │
        ▼                      ▼
   ┌──────────────────────────────────────────────┐
   │                Sprint Files                  │
   │  BACKLOG.md (own Task only)                  │
   │  HANDOFF.md (own row only)                   │
   │  active/F-auth.md | active/F-user-profile.md │
   └──────────────────────────────────────────────┘
```

### Conflict Prevention Rules

1. **Modify only your area**
   - BACKLOG.md: Only assigned Task
   - HANDOFF.md: Only your row
   - active/: Only your Feature

2. **Read latest version before modifying shared files**

3. **On conflict, retry**

### Handoff Scenario

#rook working, needs to end session → #pixel takes over:

```
# Session 1: #rook
@INSTRUCTION.md #rook
→ Start T-signup-api
→ Need to end session mid-work
→ Record state in active/F-auth.md
→ BACKLOG.md: T-signup-api `blocked` (with reason)

# Session 2: #pixel
@INSTRUCTION.md #pixel
→ Read active/F-auth.md
→ Check T-signup-api status, continue work
```

---

## Context Compaction Recovery

When context compaction occurs during a Claude Code session:

### Recovery Procedure

```
1. Re-read INSTRUCTION.md
2. Re-read BACKLOG.md
3. Re-read HANDOFF.md
4. Re-read active/F-{slug}.md (most important!)
5. Maintain same agent name
6. Continue in-progress Task
```

### Tips

- Update `active/` files frequently
- Record important decisions immediately in `refs/decisions/`
- Save progress periodically for complex logic

---

## Sprint Completion

### On Feature Completion

1. Mark Feature as `[x]` `done` in BACKLOG.md
2. Move `active/F-{slug}.md` → `refs/archive/F-{slug}.md` (preserve as-is)
3. Add **Lessons Learned** section to the archived file
4. Finalize **Decisions Made** section in the archived file
5. Sprint-wide insights only → `refs/decisions/_sprint.md` or `refs/lessons/_sprint.md`
6. Remove Feature section from BACKLOG.md, add one-line to Done:
   `F-{slug}: Feature Name - completed YYYY-MM-DD`

### On Sprint Completion

1. All Features `done`
2. Archive everything to `refs/archive/`
3. Write Sprint retrospective in `refs/lessons/_sprint.md`
