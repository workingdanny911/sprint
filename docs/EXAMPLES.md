# Sprint Plugin - Real-World Examples

## Example 1: Web API Project

### Scenario

"I want to build a user management API. Need authentication, profile, and settings features."

### Step 1: Initialize Sprint

```
User: /sprint:init

Claude: What should we call this sprint? (kebab-case recommended)

User: user-management-api

Claude: Where should we create it?
1. sprints/user-management-api/ (default)
2. Custom path

User: 1

Claude: Sprint created.

sprints/user-management-api/
├── BACKLOG.md
├── HANDOFF.md
├── INSTRUCTION.md
├── .sprint-version
├── active/
└── refs/
    ├── designs/
    ├── plans/
    ├── decisions/
    │   └── _sprint.md
    ├── lessons/
    │   └── _sprint.md
    └── archive/

Next: Use /sprint:add-backlog to add work items.
```

### Step 2: Add Features

```
User: /sprint:add-backlog

Claude: What do you want to build?

User: Authentication feature - login, signup, password reset

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

Claude: Success criteria?

User: APIs work and tests pass

Claude: [Entering Plan Mode]

Proposed structure:

### F-auth: Authentication
> User auth (login, signup, password reset)
> Social login excluded

**Type**: coding

- [ ] T-login-api: Login API `backlog`
- [ ] T-signup-api: Signup API `backlog`
- [ ] T-password-reset: Password Reset API `backlog`
- [ ] T-auth-integration-tests: Auth Integration Tests `backlog`
- [ ] T-review-auth: Review & Refactor `backlog`

Proceed?

User: Yes

Claude: Added to BACKLOG.md.

Create design doc? (2+ tasks → recommended)

User: Yes

Claude: Created refs/designs/F-auth.md
```

### Step 3: Work on Tasks

```
User: @sprints/user-management-api/INSTRUCTION.md #rook

Claude: Starting session as #rook.

[Loading context]

Current state:
- F-auth: Authentication (5 tasks)
  - T-login-api: Login API `backlog` ← Top priority
  - T-signup-api: Signup API `backlog`
  - ...

Claiming and starting T-login-api: Login API.

[Updating BACKLOG.md: T-login-api → #rook `in_progress`]
[Updating HANDOFF.md: Adding to In Progress]
[Creating active/F-auth.md]

[Entering Plan Mode - Login API implementation plan]

## Login API Implementation Plan

### T-login-api.schema: Define Schema
- POST /api/auth/login
- Request: { email, password }
- Response: { accessToken, refreshToken, user }

### T-login-api.endpoint: Implement Endpoint
- JWT token generation
- Password verification (bcrypt)

### T-login-api.validation: Add Validation
- Email format validation
- Password minimum requirements

Proceed with this plan?

User: Go ahead

[Implementation in progress...]

Claude: Completed T-login-api: Login API.

Completed:
- POST /api/auth/login endpoint
- JWT token issuance logic
- bcrypt password verification
- Input validation

Updated files:
- src/routes/auth.ts
- src/services/auth.ts
- src/validators/auth.ts

Sprint file updates:
- BACKLOG.md: T-login-api → `done`
- HANDOFF.md: Moved to Recently Done
- active/F-auth.md: Progress recorded

Ending session. For next Task:
@sprints/user-management-api/INSTRUCTION.md #rook
```

### Step 4: Next Task

```
User: @sprints/user-management-api/INSTRUCTION.md #rook

Claude: Starting session as #rook.

[Loading context]

Current state:
- F-auth: Authentication
  - [x] T-login-api: Login API `done`
  - T-signup-api: Signup API `backlog` ← Next
  - ...

Starting T-signup-api: Signup API.
```

---

## Example 2: Documentation Project

### Scenario

"I want to write documentation for existing APIs."

### Initialize Sprint

```
User: /sprint:init

Claude: Sprint name?

User: api-documentation

Claude: Created.
```

### Add Features

```
User: /sprint:add-backlog

Claude: What do you want to build?

User: API docs - auth, user, and order API documentation

Claude: Type?

User: 2 (docs)

Claude: [Plan Mode]

### F-api-docs: API Documentation
> Auth, User, Order API documentation

**Type**: docs

- [ ] T-auth-api-docs: Auth API Documentation `backlog`
- [ ] T-user-api-docs: User API Documentation `backlog`
- [ ] T-order-api-docs: Order API Documentation `backlog`
- [ ] T-review-api-docs: Review & Refactor `backlog`

User: Looks good

Claude: Added to BACKLOG.md.
```

### Work

```
User: @sprints/api-documentation/INSTRUCTION.md #danny

Claude: Starting T-auth-api-docs: Auth API Documentation.

[Writing documentation...]

Created docs/api/authentication.md.

Ending session.
```

---

## Example 3: Multi-Agent Collaboration

### Scenario

Two Claude Code sessions working simultaneously

### Setup

```
# Session 1
User: @sprints/my-sprint/INSTRUCTION.md #rook

# Session 2 (different terminal)
User: @sprints/my-sprint/INSTRUCTION.md #pixel
```

### BACKLOG.md State

```markdown
### F-auth: Authentication
- [ ] T-login-api: Login API #rook `in_progress`
- [ ] T-signup-api: Signup API `backlog`

### F-user-profile: User Profile
- [ ] T-get-profile: Get Profile API #pixel `in_progress`
- [ ] T-update-profile: Update Profile API `backlog`
```

### HANDOFF.md State

```markdown
## In Progress

| Feature | Task | Persona | Started | Notes |
|---------|------|---------|---------|-------|
| F-auth | T-login-api: Login API | #rook | 2024-01-28 | JWT impl |
| F-user-profile | T-get-profile: Get Profile API | #pixel | 2024-01-28 | Basic query |
```

### Conflict Prevention

```
# #rook modifies only F-auth related
active/F-auth.md

# #pixel modifies only F-user-profile related
active/F-user-profile.md

# BACKLOG.md: each modifies only their assigned Task
# HANDOFF.md: each modifies only their row
```

---

## Example 4: Review and Improvement

### Review After Task Completion

```
User: /sprint:review-backlog T-login-api

Claude: Starting review of T-login-api: Login API.

Type: coding
Checklist: coding/checklist.md

### Review Results

### 🔴 Critical (1)
1. **SQL Injection vulnerability**
   - Location: src/services/auth.ts:45
   - Current: Direct string concatenation
   - Impact: Security vulnerability

### 🟡 Improvement (2)
1. **Insufficient error handling**
   - Current: All errors return 500
   - Suggestion: Use appropriate HTTP status codes

2. **Missing logging**
   - Suggestion: Log login attempts

### 🟢 Minor (1)
- Type definitions could improve

Create improvement plan?

User: Yes

Claude: [Plan Mode]

### Improvement Tasks

- [ ] T-fix-login-api: Fix review issues (T-login-api) `backlog`
  - [ ] T-fix-login-api.sql-injection: Fix SQL injection vulnerability
  - [ ] T-fix-login-api.error-handling: Improve error handling
  - [ ] T-fix-login-api.logging: Add login attempt logging

Added to BACKLOG.md.
```

---

## Example 5: Breaking Down Complex Tasks

### Initial Addition

```
User: /sprint:add-backlog

Claude: What do you want to build?

User: Payment system

Claude: That's a broad scope. What specific features do you need?

User: Card payment, refunds, payment history

Claude: [Plan Mode]

### F-payment: Payment System
> Card payment, refunds, payment history

**Type**: coding

- [ ] T-payment-gateway: Payment Gateway Integration `backlog`
- [ ] T-process-payment: Process Payment API `backlog`
- [ ] T-refund-api: Refund API `backlog`
- [ ] T-payment-history: Payment History API `backlog`
- [ ] T-payment-integration-tests: Payment Integration Tests `backlog`
- [ ] T-review-payment: Review & Refactor `backlog`

T-payment-gateway looks complex. Want to design it in detail first?

User: Yes
```

### Detail Design with `/sprint:plan-backlog`

```
User: /sprint:plan-backlog T-payment-gateway

Claude: Starting design for T-payment-gateway: Payment Gateway Integration.

Which payment gateway?
1. Stripe (recommended) - Great docs, SDK provided
2. PayPal
3. Square
4. Other

User: 1

Claude: Payment methods?
1. Card only
2. Card + Bank transfer
3. All

User: 1

Claude: [Verifying understanding...]

[Plan Mode]

### T-payment-gateway: Payment Gateway Integration

- [ ] T-payment-gateway.stripe-sdk: Setup Stripe SDK
- [ ] T-payment-gateway.payment-intent: Create Payment Intent Wrapper
- [ ] T-payment-gateway.webhook: Webhook Handler Setup
- [ ] T-payment-gateway.test-mode: Test with Stripe Test Mode

Updated BACKLOG.md.
Created refs/plans/F-payment-T-payment-gateway.md.
```

---

## Example 6: Handling Blockers

### Blocker Occurs

```
User: @sprints/my-sprint/INSTRUCTION.md #danny

Claude: Starting T-notification-api: Notification API.

[Working...]

Issue found: T-notification-api depends on T-user-preferences (User Preferences).
Need to read user notification settings, but T-user-preferences is not complete.

Marking T-notification-api as blocked.

BACKLOG.md:
- [ ] T-notification-api: Notification API `blocked` → depends on T-user-preferences

HANDOFF.md Blockers:
| T-notification-api | #danny | T-user-preferences incomplete | 2024-01-28 |

Work on something else?
1. Start T-user-preferences first (recommended)
2. Pick Task from different Feature
3. End session

User: 1

Claude: Starting T-user-preferences: User Preferences API.
```

### Blocker Resolved

```
Claude: Completed T-user-preferences.

T-notification-api blocker resolved.

BACKLOG.md:
- [ ] T-notification-api: Notification API `backlog` (unblocked)

Removed T-notification-api from HANDOFF.md Blockers.

Ending session.
Start T-notification-api in next session.
```

---

## Example 7: Sprint Version Update

### After Template Update

```
User: /sprint:update-version

Claude: Checking versions...

Current Sprint version: 1.0.0
Latest template version: 1.1.0

Update needed.

Changes:
- INSTRUCTION.md: Improved Context Recovery procedure
- BACKLOG.md: Updated Status diagram

Data to preserve:
- Features: 3
- Done: 2 records
- In Progress: 1 row

Proceed?

User: Yes

Claude: Backup created: refs/archive/_backup-v1.0.0/

Update complete!

Version: 1.0.0 → 1.1.0
All user data preserved.
```

---

## Example 8: Agent Teams (Feature Assignment)

### Scenario

User assigns a Feature with multiple independent Tasks. The agent uses agent teams to parallelize work.

### Difference from Example 3

- **Example 3**: Separate Claude Code sessions (multi-agent via files)
- **Example 8**: Single session with sub-agents (agent teams via `TeamCreate`)

### Step 1: Feature Assignment

```
User: @sprints/my-sprint/INSTRUCTION.md #rook, work on Feature F-user-auth

Claude: Starting session as #rook.

[Loading context]

F-user-auth: User Authentication (5 tasks)
- [ ] T-login-api: Login API `backlog`
- [ ] T-signup-api: Signup API `backlog`
- [ ] T-password-reset: Password Reset API `backlog`
- [ ] T-auth-integration-tests: Auth Integration Tests `backlog`
- [ ] T-review-user-auth: Review & Refactor `backlog`

My assignment is Feature F-user-auth.
```

### Step 2: Parallelism Assessment (Session Start Step 5)

```
Claude: Assessing parallelism for F-user-auth...

Dependency analysis:
- T-login-api (Login), T-signup-api (Signup), T-password-reset (Password Reset) → independent of each other
- T-auth-integration-tests (Integration Tests) → depends on the three above
- T-review-user-auth (Review) → depends on all above

T-login-api, T-signup-api, T-password-reset are independent — agent teams would improve speed.

Proposed team:

| Agent | Persona | Role | Tasks |
|-------|---------|------|-------|
| lead (me) | #rook | Login + coordination | T-login-api |
| worker-1 | #pixel | Signup | T-signup-api |
| worker-2 | #dash | Password Reset | T-password-reset |

Execution plan:
1. Parallel phase: T-login-api, T-signup-api, T-password-reset simultaneously
2. Sequential phase: T-auth-integration-tests — lead handles after merge
3. Completion: T-review-user-auth (review) — lead handles

Create agent team?

User: Yes
```

### Step 3: Team Execution

```
Claude: [TeamCreate: feature-F-user-auth]
[TaskCreate: T-login-api, T-signup-api, T-password-reset]
[Spawning #pixel (worker-1), #dash (worker-2)]
[Assigning T-signup-api → #pixel, T-password-reset → #dash]

Starting T-login-api: Login API...

[Working on T-login-api while teammates work in parallel]
```

### Step 4: Teammate Completion

```
#pixel → lead: T-signup-api Signup API complete. Files: src/routes/signup.ts,
  src/services/signup.ts. All validation implemented.

Claude: Reviewing #pixel's work on T-signup-api...
[Updates BACKLOG.md: T-signup-api → review]
[Updates active/F-user-auth.md with T-signup-api progress]

[SendMessage to #pixel: shutdown_request]
```

### Step 5: Sequential Phase + Completion

```
Claude: T-login-api, T-signup-api, T-password-reset all complete. Teammates shut down.

Starting T-auth-integration-tests: Auth Integration Tests...

[Working on integration tests...]

T-auth-integration-tests complete. All tasks in F-user-auth verified.

Sprint file updates:
- BACKLOG.md: T-login-api, T-signup-api, T-password-reset, T-auth-integration-tests → `review`
- HANDOFF.md: F-user-auth moved to In Review
- active/F-user-auth.md: Full completion notes

Feature F-user-auth: User Authentication is ready for review.

Ending session.
```

---

## Example 9: Worktree Mode

### Scenario

"Feature별로 독립된 worktree에서 작업하고 싶다."

### Step 1: Initialize Sprint (Worktree Mode)

```
User: /sprint:init

Claude: Sprint name?
User: payment-api

Claude: Location?
User: sprints/payment-api/

Claude: Mode?
1. default - Sprint and code in same directory
2. worktree - Each Feature gets its own git worktree

User: 2

Claude: How should worktrees be created?
1. Default (git worktree add) - recommended
2. Custom command

User: 1

Claude: Where should worktrees be placed?
Suggested: ../payment-api-worktrees/

User: OK

Claude: Sprint created (worktree mode).
- Added sprints/ to .gitignore
- Sprint files are not git-tracked
- .sprint-config created:
  {
    "mode": "worktree",
    "sprintRoot": "/Users/.../sprints/payment-api/",
    "worktree": {
      "command": "git worktree add {path} -b {branch}",
      "pathPrefix": "../payment-api-worktrees"
    }
  }
```

### Step 2: Add Feature with Worktree

```
User: /sprint:add-backlog

Claude: What do you want to build?
User: Payment processing

[... brainstorming ...]

Claude: [Plan Mode]

Worktree for this Feature:
1. Create new worktree (default)
2. Use existing worktree

User: 1

Claude: Suggested:
- Branch: feature/F-payment
- Worktree: ../payment-api-worktrees/F-payment/

User: OK

### F-payment: Payment Processing
> Card payment and refund handling

**Branch**: feature/F-payment
**Worktree**: /Users/.../payment-api-worktrees/F-payment/

- [ ] T-payment-gateway-setup: Payment Gateway Setup `backlog`
- [ ] T-process-payment: Process Payment API `backlog`
- [ ] T-refund-api: Refund API `backlog`
- [ ] T-review-payment: Review & Refactor `backlog`
- [ ] T-merge-payment: Merge feature/F-payment and clean up worktree `backlog`

Claude: Worktree created. Feature added to BACKLOG.md.
```

### Step 3: Work in Worktree

```
User: @sprints/payment-api/INSTRUCTION.md #rook

Claude: Starting session as #rook.

[Sprint Root: /Users/.../sprints/payment-api/]
[Reading BACKLOG.md, HANDOFF.md from Sprint Root]

F-payment: Payment Processing
- Worktree: /Users/.../payment-api-worktrees/F-payment/
- T-payment-gateway-setup: Payment Gateway Setup ← Top priority

Claiming T-payment-gateway-setup. Working in worktree: /Users/.../payment-api-worktrees/F-payment/

[Code work in worktree, sprint updates in Sprint Root]
```

### Step 4: Merge (Final Task)

```
User: @sprints/payment-api/INSTRUCTION.md #rook

Claude: Starting session as #rook.

T-merge-payment: Merge feature/F-payment and clean up worktree

Working from main worktree (Sprint Root):

Steps:
1. git merge feature/F-payment  ← from main worktree
2. git worktree remove /Users/.../payment-api-worktrees/F-payment/
3. git branch -d feature/F-payment

[Executing merge...]

Merge complete. Worktree removed. F-payment done.
```
