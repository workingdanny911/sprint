# Sprint Plugin - Core Concepts

## 1. Hierarchical Work Structure

The Sprint plugin manages work in a 3-level hierarchy.

### Feature (F-{slug})

- **Definition**: An independent unit of functionality that provides value to users
- **Examples**: "User Authentication System", "Payment Module", "Dashboard"
- **Characteristics**:
  - Composed of multiple Tasks
  - Design document recommended (`refs/designs/F-{slug}.md`)
  - Moved to archive when complete

```markdown
### F-user-auth: User Authentication
> User login/signup functionality

**Design**: [refs/designs/F-user-auth.md](refs/designs/F-user-auth.md)

- [ ] T-login-api: Login API `backlog`
- [ ] T-signup-api: Signup API `backlog`
- [ ] T-token-refresh: Token Refresh `backlog`
- [ ] T-review-user-auth: Review & Refactor `backlog`
```

### Task (T-{task-slug})

- **Definition**: A unit of work completable in a single Claude Code session
- **Examples**: "Implement Login API", "Write tests", "Update documentation"
- **Key Constraint**: **Must be completable in one session**
- **Types**:
  - `coding` - Software development
  - `docs` - Documentation
  - `ideation` - Ideas/design
  - `general` - Other

```markdown
- [ ] T-login-api: Login API `backlog`
- [ ] T-signup-api: Signup API #danny `in_progress`
- [x] T-token-refresh: Token Refresh `done`
```

### Sub-task (T-{task-slug}.{sub-slug})

- **Definition**: A Task broken down into smaller steps
- **Examples**: "Define schema", "Implement endpoint", "Add validation"
- **Purpose**: Track progress on complex Tasks

```markdown
- [ ] T-login-api: Login API `in_progress`
  - [x] T-login-api.schema: Define request/response schema
  - [ ] T-login-api.endpoint: Implement endpoint
  - [ ] T-login-api.validation: Add validation
```

---

## 2. Status

### Status Transition Diagram

```
backlog ──────► in_progress ──────► review ──────► done
                    │                  │
                    ▼                  │
                 blocked ◄─────────────┘
                    │
                    ▼
                 backlog (unblocked)
```

### Status Descriptions

| Status | Meaning | Notation |
|--------|---------|----------|
| `backlog` | Not started, anyone can claim | `\`backlog\`` |
| `in_progress` | In progress, agent assigned | `#agent \`in_progress\`` |
| `blocked` | Cannot proceed, waiting on dependency | `\`blocked\`` |
| `review` | Work complete, **mandatory** before done — awaiting user review | `\`review\`` |
| `done` | User reviewed and approved | `[x]` `\`done\`` |

---

## 3. Agent

### What is an Agent?

- One Claude Code session = one agent
- Identified by `#persona-name` (e.g., `#rook`, `#pixel`, `#dash`)

### Persona

A persona is a named character template with:

| Field | Description | Examples |
|-------|-------------|----------|
| `decision_style` | How the agent makes decisions | pragmatist, methodical, experimental, conservative, analytical, critical |
| `communication` | How the agent communicates | concise, detailed, enthusiastic, measured, socratic, direct |
| `domain` | Area of expertise | backend, frontend, fullstack, infra/data, discussion, review |

**Preset Personas:**

| Name | Style | Tone | Domain | Character |
|------|-------|------|--------|-----------|
| rook | pragmatist | concise | backend | Ships fast, codes over talks |
| pixel | methodical | detailed | frontend | Detail-obsessed, edge case hunter |
| dash | experimental | enthusiastic | fullstack | Rapid prototyper, "let's try it" |
| slate | conservative | measured | infra/data | "Can you roll that back?" |
| echo | analytical | socratic | discussion | Answers with questions |
| thorn | critical | direct | review | High standards, never wrong |

**Loading**: `personas/{name}.md` is read at session start. Additive to INSTRUCTION.md rules.

**Auto-matching**: When no name given, agent analyzes the task and proposes a matching persona.

### Agent Session Lifecycle

```
┌─────────────────────────────────────────────────────────────┐
│                     Session Lifecycle                       │
└─────────────────────────────────────────────────────────────┘

  @INSTRUCTION.md #rook
         │
         ▼
  ┌──────────────┐
  │ Load Persona │ ◄── Read personas/{name}.md (if exists)
  └──────────────┘
         │
         ▼
  ┌─────────────┐
  │ Load Context │ ◄── Read BACKLOG, HANDOFF, active/
  └─────────────┘
         │
         ▼
  ┌──────────────────┐
  │ Determine Scope  │ ◄── Feature? Assess parallelism → Teams or narrow to Task
  │                  │     Task? Proceed. None? Claim highest priority.
  └──────────────────┘
         │
         ▼
  ┌─────────────┐
  │ Execute Task │ ◄── Do work, update active/
  └─────────────┘
         │
         ▼
  ┌─────────────┐
  │ Mark Review │ ◄── Update BACKLOG, HANDOFF (status: review)
  └─────────────┘
         │
         ▼
  ┌─────────────┐
  │ End Session │ ◄── Report to user, do NOT auto-start next Task
  └─────────────┘
```

### Agent Teams (Within a Session)

Multi-agent workflow coordinates **separate sessions** via files. Agent teams run **within a single session** using `TeamCreate`.

Agent teams serve two purposes:

| Purpose | Example |
|---------|---------|
| **Speed** | Independent sub-items run in parallel |
| **Quality** | Separate agents for implementation vs testing, cross-verification |

Agent teams apply at any scope:

```
Feature Assignment (Tasks in parallel)         Task Assignment (Sub-tasks in parallel)
┌────────────────────────────────────────┐    ┌──────────────────────────────────────────────────────┐
│ lead       worker-1      worker-2       │    │ lead              worker-1           worker-2          │
│ T-login    T-signup      T-pwd-reset    │    │ T-login.schema    T-login.endpoint   T-login.validation│
│   └──────────┼─────────────┘            │    │   └──────────────────┼──────────────────┘             │
│              ▼                           │    │                      ▼                                 │
│    Lead merges, continues                │    │              Merge results                             │
│    with T-auth-tests, T-review-user-auth │    └──────────────────────────────────────────────────────┘
└────────────────────────────────────────┘
```

**Use when:** 2+ independent sub-items (speed), or independent perspectives needed (quality).
**Skip when:** Sequential dependencies, simple work, coordination overhead exceeds benefit.
**User approval:** Always required before creating teams.
**Without teams:** When a Feature is assigned but agent teams are skipped, the agent narrows scope to a single Task — the highest-priority incomplete Task within that Feature. The remaining Tasks are left for subsequent sessions.

---

## 4. Core Files

### BACKLOG.md

- **Role**: Single Source of Truth for all work items
- **Contents**: Feature list, Task list, status, priority
- **Modified When**: Task claim, status change, Feature addition

### HANDOFF.md

- **Role**: Real-time view of current progress
- **Contents**: In Progress, In Review, Blockers, Dependencies
- **Modified When**: Work start/end, blocker occurrence

### INSTRUCTION.md

- **Role**: Agent guidelines
- **Contents**: Session start/end procedures, persona system, conflict prevention
- **Modification**: Rarely (only on template updates)

### active/F-{slug}.md

- **Role**: Working context for in-progress Features
- **Contents**: Current state, decisions, next steps, notes
- **Lifecycle**: Created when Feature starts → Deleted when complete

### refs/ Folder

| Folder | Purpose | Lifecycle |
|--------|---------|-----------|
| `designs/` | Feature design docs | With Feature |
| `plans/` | Task execution plans | Archive after Task completion |
| `decisions/` | Decision records | Permanent |
| `lessons/` | Lessons learned | Permanent |
| `archive/` | Completed Features | Permanent |

---

## 5. Naming System

```
F-{feature-slug}        - Feature: kebab-case slug from the Feature name (F-user-auth, F-payment...)
T-{task-slug}           - Task: kebab-case slug from the Task content, no Feature prefix (T-login-api, T-refund-api...)
T-{task-slug}.{sub-slug} - Sub-task: one level under a Task (T-login-api.schema, T-login-api.endpoint...)
```

### Example

```
F-payment                  = Feature "Payment System"
T-payment-gateway          = Task "Payment Gateway Integration"
T-payment-gateway.webhook  = Sub-task "Webhook Handler Setup" under T-payment-gateway
```

### Slug Rules

- **Feature slug**: kebab-case derived from the Feature name (`Payment System` → `F-payment`). On collision with an existing Feature slug, confirm a different name with the user.
- **Task slug**: kebab-case derived from the Task content, **globally unique across the whole sprint** with **no Feature prefix** (`Login API` → `T-login-api`, not `T-user-auth-login`). On collision with any existing Task slug, confirm a different name with the user.
- **Sub-task slug**: one `.{sub-slug}` segment appended to the parent Task slug (`T-login-api.schema`). One level only.
- **R&R Task**: slug `T-review-{feature-slug}` (e.g. `T-review-user-auth`), title kept as "Review & Refactor".
- No sequential numbering — slugs are descriptive, not positional. There is no max+1 increment.

---

## 6. Priority

### Notation

1. **Position**: Higher = higher priority
2. **URGENT**: `[URGENT]` prefix for time-sensitive items

```markdown
### [URGENT] F-critical-bugfix: Critical Bug Fix   ← Highest priority
> Urgent fix needed

### F-dashboard: User Dashboard                     ← Second priority
> New feature

### F-docs: Documentation                           ← Third priority
> Documentation
```

### Claiming Rules

1. `[URGENT]` items first
2. Then top-to-bottom order
3. Check dependencies (not blocked)

---

## 7. Review & Refactoring

### Review & Refactor Task

Every Feature includes a final review/refactor Task:

```markdown
- [ ] T-login-api: Login API `backlog`
- [ ] T-signup-api: Signup API `backlog`
- [ ] T-review-user-auth: Review & Refactor `backlog`  ← Required
```

### When to Add Review Tasks

| Situation | Task to Add |
|-----------|-------------|
| Feature completion | `T-review-{feature-slug}: Review & Refactor` |
| Complex Task (3+ sub-tasks) | `T-review-{task-slug}: Review` |
| Integration points | `T-review-integration: Review integration` |

### Review Contents

- Functionality verification (confirm it works)
- Bug/edge case checks
- Code refactoring
- Remove unnecessary code/comments
- Codebase pattern consistency

---

## 8. Automated Feature Execution

### What is `/sprint:work-on-feature`?

An orchestrator skill that creates an **Agent Team** to automatically execute all Tasks in a Feature — with bidirectional communication between Lead, Workers, and Verifiers.

### Manual vs Automated Execution

| | Manual (`@INSTRUCTION.md`) | Automated (`/sprint:work-on-feature`) |
|-|---------------------------|--------------------------------------|
| Session control | User starts each session | Lead manages Agent Team |
| Architecture | Independent sessions | Agent Team (TeamCreate + SendMessage) |
| Task selection | Agent claims from backlog | Lead assigns via shared task board |
| Review | User triggers `/review-work` | Lead directs Worker to run review, selects "all" |
| Verification | User triggers `/review-backlog` | Verifier teammate (thorn) auto-verifies |
| Communication | File-based handoff | Bidirectional SendMessage (Lead ↔ Worker ↔ Verifier) |
| Sprint files | Agent updates | Lead (orchestrator) updates |
| R&R Task | Manual session | Auto — ends at `review` for user approval |

### Execution Flow

```
/sprint:work-on-feature F-{slug}
    │
    ▼
Briefing? ──Yes──► /explain
    │
    ▼
Execution Plan (persona matching + batch grouping)
    │
    ▼
User Approval
    │
    ▼
TeamCreate("feature-F-{slug}")
    │
    ▼
┌──────────────── Batch Loop ────────────────┐
│  Lead ──SendMessage──► Worker: "implement"  │
│       ◄──SendMessage── Worker: "done"       │
│  Lead ──SendMessage──► Worker: "review-work"│
│       ◄──SendMessage── Worker: findings     │
│  Lead ──SendMessage──► Worker: "fix all"    │
│       ◄──SendMessage── Worker: fixed        │
│       │                                     │
│  Lead spawns Verifier teammate              │
│  Verifier ◄──SendMessage──► Worker (Q&A)    │
│  Verifier ──SendMessage──► Lead: PASS/FAIL  │
│       │                                     │
│  Lead: shutdown Worker + Verifier           │
│  Lead: learning ──► refs/lessons/           │
└─────────────────────────────────────────────┘
    │
    ▼
R&R teammate (thorn) ──► /review-backlog (immediate-fix)
    │
    ▼
Broadcast shutdown ──► all teammates
    │
    ▼
Task → review (user final approval)
```

---

## 9. Context Preservation

### Problem: Knowledge Loss Between Sessions

Claude Code sessions are independent → no knowledge of previous sessions

### Solution: Structured Documentation

```
┌─────────────────────────────────────────────────────────────┐
│  Session 1              Session 2              Session 3    │
│     │                      │                      │         │
│     ▼                      ▼                      ▼         │
│ ┌───────┐             ┌───────┐             ┌───────┐       │
│ │ Task A │             │ Task B │             │ Task C │      │
│ └───────┘             └───────┘             └───────┘       │
│     │                      │                      │         │
│     └──────────────────────┼──────────────────────┘         │
│                            │                                │
│                            ▼                                │
│                    ┌────────────────────┐                   │
│                    │ active/F-user-auth.md │ ◄── Shared context│
│                    │ refs/decisions/       │                   │
│                    │ refs/lessons/         │                   │
│                    └────────────────────┘                   │
└─────────────────────────────────────────────────────────────┘
```

### What to Record

| Type | Location | Example |
|------|----------|---------|
| Progress | `active/F-{slug}.md` | "T-signup-api done, T-token-refresh in progress" |
| Decisions | `refs/decisions/` | "JWT vs Session → chose JWT" |
| Lessons | `refs/lessons/` | "Need to unify API response format" |
| Blockers | `HANDOFF.md` | "T-notification-api blocked: DB schema undefined" |

---

## 10. Worktree Mode

### What is Worktree Mode?

Git worktree를 활용해 Feature별로 독립된 코드 작업 공간을 제공하는 모드.

### Default Mode vs Worktree Mode

| | Default | Worktree |
|-|---------|----------|
| Code location | Sprint과 같은 디렉토리 | Feature별 독립 worktree |
| Sprint files | 상대 경로 | Sprint Root 절대 경로 |
| Git tracking | Sprint files tracked | Sprint files gitignored |
| Branch strategy | 자유 | Feature당 1 branch |
| Isolation | 없음 | Feature별 코드 격리 |

### Architecture

```
Main Worktree (Sprint Root)          Feature Worktrees
┌────────────────────────┐          ┌─────────────────────────┐
│ sprints/my-sprint/     │          │ ../worktrees/F-user-auth/ │
│   ├── BACKLOG.md       │ ◄─────  │   ├── src/              │
│   ├── HANDOFF.md       │  read/  │   └── ...               │
│   ├── INSTRUCTION.md   │  write  └─────────────────────────┘
│   ├── active/          │          ┌─────────────────────────┐
│   └── refs/            │ ◄─────  │ ../worktrees/F-payment/   │
│                        │  read/  │   ├── src/              │
│ .gitignore             │  write  │   └── ...          │
│   └── sprints/         │          └────────────────────┘
└────────────────────────┘
```

### Agent Rules (Worktree Mode)

1. Code → own worktree only
2. Sprint files → Sprint Root only
3. Other worktrees → access forbidden
