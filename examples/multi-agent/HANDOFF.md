# Work Board

> Real-time view of current work. Full backlog in BACKLOG.md.

---

## Before You Start (REQUIRED)

- [ ] Read `BACKLOG.md` - Find your assigned or available tasks
- [ ] Read `refs/decisions/F-{slug}-*.md` - If working on a Feature
- [ ] Check `active/F-{slug}.md` - Feature context (if exists)
- [ ] Check **Dependencies** below - Ensure nothing blocks your task

---

## WIP Limit: 1 task per agent

Each agent may only have **one task in_progress** at a time.

---

## Team

| Agent | Focus Area | Status |
|-------|-----------|--------|
| #danny | F-payment: Payment | Active |
| #agent-1 | F-order: Order | Active |

---

## In Progress

| Feature | Task | Agent | Started | Notes |
|---------|------|-------|---------|-------|
| F-payment | T-payment-intent: Create Payment Intent | #danny | 2024-01-28 | Stripe API integration |
| F-order | T-create-order: Create Order API | #agent-1 | 2024-01-28 | DB transaction handling |

---

## In Review

| Feature | Task | Agent | Reviewer | Notes |
|---------|------|-------|----------|-------|
| | | | | |

---

## Recently Done

> Last 5 items.

| Feature | Task | Agent | Completed | Notes |
|---------|------|-------|-----------|-------|
| F-order | T-order-model: Order Model | #agent-1 | 2024-01-28 | With migration |
| F-payment | T-stripe-sdk: Stripe SDK | #danny | 2024-01-27 | Test mode configured |

---

## Dependencies

> Feature/Task dependencies. Check before starting work.

| Item | Depends On | Status | Notes |
|------|-----------|--------|-------|
| T-cancel-order | T-webhook-handler | T-webhook-handler backlog | Needs Webhook for refund |
| T-order-confirmation-email | T-create-order | T-create-order in_progress | Email after order complete |
| T-payment-receipt-email | T-payment-confirmation | T-payment-confirmation backlog | Receipt after payment |
| F-notification | F-payment, F-order | Partially complete | Needs some F-payment, F-order complete |

---

## Blockers

| Task | Agent | Blocker | Since |
|------|-------|---------|-------|
| T-cancel-order | - | T-webhook-handler incomplete (Webhook) | 2024-01-28 |
| T-order-confirmation-email | - | T-create-order incomplete (Order API) | 2024-01-28 |
| T-payment-receipt-email | - | T-payment-confirmation incomplete (Payment confirm) | 2024-01-28 |

---

## Coordination Notes

### #danny ↔ #agent-1 Collaboration Points

1. **T-webhook-handler (Webhook) → T-cancel-order (Cancel Order)**
   - #danny notifies #agent-1 when T-webhook-handler completes
   - Need prior agreement on refund logic interface

2. **T-create-order (Create Order) → T-payment-intent (Payment Intent)**
   - Payment integration after order creation
   - Order ID format agreed: UUID v4

---

## Quick Actions

### Claim a task
1. Find unclaimed task in BACKLOG.md (no #agent)
2. Add `#yourname`, change status to `in_progress`
3. Add entry to **In Progress** table
4. Check Dependencies - verify not blocked

### Complete a task
1. Move from **In Progress** to **Recently Done**
2. Update BACKLOG.md: mark `[x]`, status to `done`
3. **Check if this unblocks other tasks** - Update Dependencies
4. **END SESSION**

### Handle blocker
1. Add to **Blockers** table
2. Document blocking task
3. Do NOT claim new task
4. Coordinate with blocking agent if needed
