---
name: add-backlog
description: Add backlog items to an existing sprint through guided brainstorming and planning. Automatically detects sprint context or asks for sprint location. Includes brainstorming phase, plan mode for task design, and ensures tasks are sized for single Claude Code sessions. Triggers include "add backlog", "new feature", "add task", "백로그 추가", "기능 추가", "태스크 추가".
---

# Add Backlog Items

Add work items to a sprint through guided brainstorming and structured planning.

## Overview

This skill guides you through:
1. **Brainstorming** - Understand what to build
2. **Planning** - Design Feature/Task structure
3. **Adding** - Update BACKLOG.md with new items

**Key constraint**: Each Task must be completable in a single Claude Code session.

---

## Workflow

### Phase 1: Find Sprint

1. Check current directory for sprint files (BACKLOG.md, HANDOFF.md)
2. If found, confirm: "Add to this sprint?"
3. If not found, ask: "Which sprint? (path or name)"

### Phase 1.5: Detect Mode (Worktree Sprint Only)

1. Read `.sprint-config` in sprint directory
2. If `"mode": "worktree"`:
   - Continue to Phase 2 with worktree awareness
   - Worktree selection happens in Phase 3 (Plan Mode)
3. If `"mode": "default"` or no config:
   - Continue with existing workflow (no change)

### Phase 2: Brainstorming (Built-in)

Use conversational discovery to understand what to build.

**Principles:**
- One question at a time
- Multiple choice when possible
- YAGNI ruthlessly - remove unnecessary scope

**Discovery flow:**

1. **What are we building?**
   - "What do you want to add to the backlog?"
   - Let user describe freely

2. **Type identification**
   - What type of work is this?
   - `coding` - Software development, writing code
   - `docs` - Documentation, guides, technical writing
   - `ideation` - Brainstorming, exploring ideas
   - `general` - Research, planning, other tasks

3. **Scope clarification**
   - What's included?
   - What's explicitly NOT included?
   - Any constraints or dependencies?

4. **Success criteria**
   - How do we know when it's done?
   - What does "good" look like?

5. **Load type-specific guidelines**
   - Read `assets/{type}/guidelines.md`
   - Apply type-specific considerations

### Phase 3: Plan Mode

Enter Plan Mode to design the work structure.

**Feature slug assignment:**
1. Derive a slug from the Feature name in kebab-case (e.g., "User Authentication" → `user-auth`)
2. Read BACKLOG.md to check for an existing Feature with the same slug
3. If the slug already exists: **stop and ask the user for a different Feature name** — slugs must be unique within the sprint
4. The Feature ID is `F-{feature-slug}` (e.g., `F-user-auth`)

**Task slug assignment:**
1. Derive each Task slug from the Task content in kebab-case (e.g., "Login API" → `login-api`)
2. Task slugs are **globally unique** across the entire sprint — no two Tasks (in any Feature) may share a slug, and there is **no Feature prefix**
3. If a slug collides with an existing Task: ask the user to confirm a distinct slug
4. The Task ID is `T-{task-slug}` (e.g., `T-login-api`); a Sub-task is `T-{task-slug}.{sub-slug}` (e.g., `T-login-api.schema`)

> No sequential numbering. Features and Tasks are identified by descriptive slugs, not `max+1` numbers.

**Task sizing rule (CRITICAL):**
> Each Task must be completable in a **single Claude Code session**.
> If too big, break into smaller Tasks.
>
> Ask yourself: "Can an agent complete this without session reset?"
> - Yes → Good size
> - No → Break it down further

**Design the structure:**

```markdown
### F-{feature-slug}: {Feature Name}
> {Brief description}

**Design**: [refs/designs/F-{feature-slug}.md](refs/designs/F-{feature-slug}.md)
**Context**: [active/F-{feature-slug}.md](active/F-{feature-slug}.md)

- [ ] T-{task-slug}: {Task name} `backlog`
  - [ ] T-{task-slug}.{sub-slug}: {Sub-task if needed}
- [ ] T-{another-slug}: {Task name} `backlog`
- [ ] T-review-{feature-slug}: Review & Refactor {Feature Name} `backlog`  ← verification/refactoring
```

> **Note**: Type, Goals, and detailed design information go in the Design doc, not BACKLOG.md.

**Review & Refactor tasks (REQUIRED):**

Task cycle: `ideation → plan → implement → verify/refactor → ... → done → final review/refactor`

| Situation | Task to add |
|-----------|-------------|
| End of every Feature | `Review & Refactor {Feature Name}` (slug `T-review-{feature-slug}`) - verify, improve, clean up |
| After complex Task (3+ sub-tasks) | `Review {task name}` (slug `T-review-{task-slug}`) - intermediate check & improve |
| Integration points | `Review integration` (slug `T-review-integration`) - verify + refactor if needed |

> R&R Tasks must keep "Review & Refactor" in the title — `/sprint:work-on-feature` identifies the final R&R Task by its title, not its slug.

**What Review & Refactor includes:**
- Verify functionality works as expected
- Check for bugs, edge cases
- Refactor messy code / improve structure
- Clean up unnecessary comments, dead code
- Ensure consistency with codebase patterns

Examples:
```markdown
- [ ] T-login-api: Login API `backlog`
- [ ] T-signup-api: Signup API `backlog`
- [ ] T-review-auth-apis: Review login/signup APIs `backlog`  ← intermediate verification
- [ ] T-token-refresh: Token refresh `backlog`
- [ ] T-review-user-auth: Review & Refactor User Auth `backlog`  ← end-of-Feature review/refactor
```

**Present to user:**
- Show proposed structure
- Highlight task sizing
- **Show verification tasks** - explain why they're placed there
- Ask for approval or adjustments

#### Worktree Selection (Worktree Mode Only)

When sprint is in worktree mode:

1. Read `.sprint-config` → get `worktree.command` and `worktree.pathPrefix`

2. **Worktree for this Feature**:
   - Show existing worktrees: `git worktree list`
   - Options:
     a. Create new worktree (default)
     b. Use existing worktree → select from list

3. **If creating new worktree**:
   - Use command from `.sprint-config` (`worktree.command`)
     - No need to ask user — already configured at init
   - Suggest branch name: `feature/F-{feature-slug}`
   - Derive worktree path from `worktree.pathPrefix` + `F-{feature-slug}`

4. **Record in Feature**:
   - Add `Branch:` and `Worktree:` fields to Feature in BACKLOG.md

#### Merge Task (Worktree Mode Only, REQUIRED)

Every Feature in worktree mode MUST have a final merge Task:

```markdown
- [ ] T-merge-{feature-slug}: Merge {branch-name} and clean up worktree `backlog`
```

This Task:
- Merges the Feature branch into the base branch
- Removes the worktree (`git worktree remove`)
- Is always the LAST Task (after Review & Refactor)

Task order:
```
- [ ] T-login-api: ... `backlog`
- [ ] T-signup-api: ... `backlog`
- [ ] T-review-user-auth: Review & Refactor User Auth `backlog`           ← review/refactor
- [ ] T-merge-user-auth: Merge feature/F-user-auth and clean up worktree `backlog`  ← merge (last)
```

#### Agent Teams Consideration

When designing the Feature structure, evaluate whether Tasks would benefit from agent teams:

For speed:
- Feature has 3+ independent Tasks that can run in parallel
- Tasks don't share file dependencies

For quality:
- Implementation and testing can be split across agents for independent perspectives
- Cross-verification would improve reliability (e.g., security-critical features)

If applicable, include in the proposal to user:
- Which Tasks can run in parallel vs. must be sequential
- Suggested team composition (agent roles and purpose)

If agent teams are **not** applicable (sequential dependencies or simple scope):
- Note that a solo agent will work **one Task per session**
- Ensure each Task is properly sized for single-session completion
- Document recommended Task order (priority/dependency) in the design

> Agent teams execute during `@INSTRUCTION.md`, not during this skill.
> This step only identifies opportunities and documents them in the design.

### Phase 4: Add to Backlog

After user approval:

1. **Update BACKLOG.md**
   - Add new Feature section with Design and Context links
   - Task list only (no detailed information)
   - Maintain priority ordering (new items at bottom unless specified)

2. **Create Design doc** (`refs/designs/F-{feature-slug}.md`)
   - Use FEATURE-DESIGN-TEMPLATE.md
   - Include Type (coding/docs/ideation/general)
   - Fill in Goals, Non-Goals, Design approach from brainstorming
   - Record any decisions made during brainstorming

3. **Create Active context** (`active/F-{feature-slug}.md`)
   - Use ACTIVE-FEATURE-TEMPLATE.md
   - Set Phase to "design" or "implementation"
   - Link to Design doc in Related Files

4. **Create worktree** (worktree mode, if "create new" selected)
   - Execute worktree creation command (default or custom from `.sprint-config`)
   - Verify worktree was created successfully
   - Record worktree path in BACKLOG.md Feature fields

5. **Confirm addition**
   ```
   Added to BACKLOG.md:
   - F-{feature-slug}: {Feature Name}
     - T-{task-slug}: {Task}
     - T-{another-slug}: {Task}

   Created:
   - refs/designs/F-{feature-slug}.md (design doc)
   - active/F-{feature-slug}.md (working context)

   Ready to work? Start with: @INSTRUCTION.md #your-name
   ```

### Phase 5: Next Step (Optional)

After confirming addition, offer to detail-plan Tasks:

```
Would you like to detail-plan any of the new Tasks now?
1. Yes → Which Task? (runs /sprint:plan-backlog)
2. No → End skill
```

If user selects Yes:
- Ask which Task to plan
- Invoke `/sprint:plan-backlog` for the selected Task
- This breaks down the Task into Sub-tasks with detailed implementation plan

If user selects No:
- End the skill
- User can run `/sprint:plan-backlog` later when needed

---

## Task Sizing Guidelines

### Good Task Size (Single Session)
- Implement one API endpoint
- Write tests for one component
- Create one documentation page
- Fix one specific bug
- Add one UI component

### Too Big (Break Down)
- "Implement authentication system" → Break into: login API, signup API, token refresh, etc.
- "Write all API documentation" → Break into: one doc per endpoint group
- "Refactor the entire module" → Break into: one file or one pattern at a time

### Rule of Thumb
- Can describe what to do in 2-3 sentences? Good size.
- Need a paragraph to explain? Too big.
- "Just do X" where X is clear? Good size.
- "Do X, Y, Z, and handle A, B, C"? Too big.

---

## Assets

Type-specific guidelines:
- `assets/coding/guidelines.md` - For software development tasks
- `assets/docs/guidelines.md` - For documentation tasks
- `assets/ideation/guidelines.md` - For brainstorming/exploration tasks
- `assets/general/guidelines.md` - For general tasks
