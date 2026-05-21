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
F-{feature-slug}        - Feature (F-payment, F-order, F-notification...)
T-{task-slug}           - Task: content-based, globally unique (T-payment-intent...)
T-{task-slug}.{sub-slug} - Sub-task (T-login-api.schema...)
```

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

---

## Features

### [URGENT] F-payment: Payment Integration
> Stripe payment integration (urgent - launch deadline)

**Design**: [refs/designs/F-payment.md](refs/designs/F-payment.md)
**Context**: [active/F-payment.md](active/F-payment.md)

- [x] T-stripe-sdk: Stripe SDK Setup `done`
- [ ] T-payment-intent: Create Payment Intent API #danny `in_progress`
- [ ] T-webhook-handler: Webhook Handler `backlog`
- [ ] T-payment-confirmation: Payment Confirmation Flow `backlog`
- [ ] T-review-payment: Review & Refactor `backlog`

---

### F-order: Order Management
> Order creation, retrieval, cancellation

**Design**: [refs/designs/F-order.md](refs/designs/F-order.md)
**Context**: [active/F-order.md](active/F-order.md)

- [x] T-order-model: Order Model & Migration `done`
- [ ] T-create-order: Create Order API #agent-1 `in_progress`
- [ ] T-get-order: Get Order API `backlog`
- [ ] T-cancel-order: Cancel Order API `blocked` → depends on T-webhook-handler (refund logic)
- [ ] T-review-order: Review & Refactor `backlog`

---

### F-notification: Notification System
> Email, push notifications

**Design**: [refs/designs/F-notification.md](refs/designs/F-notification.md)

- [ ] T-email-service: Email Service Setup `backlog`
- [ ] T-order-confirmation-email: Order Confirmation Email `blocked` → depends on T-create-order
- [ ] T-payment-receipt-email: Payment Receipt Email `blocked` → depends on T-payment-confirmation
- [ ] T-review-notification: Review & Refactor `backlog`

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
