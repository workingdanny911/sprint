# F-order: Order Management - Active Context

> Owner: #agent-1

## Current Status

- **Task**: T-create-order: Create Order API
- **Started**: 2024-01-28

## Progress

### Completed
- [x] T-order-model: Order Model & Migration
  - Order table created
  - OrderItem table created
  - Status: pending, paid, cancelled, refunded

### In Progress
- [ ] T-create-order: Create Order API
  - [x] POST /api/orders route
  - [x] Input validation
  - [ ] Transaction handling
  - [ ] Inventory check logic

### Remaining
- [ ] T-get-order: Get Order API
- [ ] T-cancel-order: Cancel Order API (blocked - waiting for T-webhook-handler)
- [ ] T-review-order: Review & Refactor

## Decisions

| Decision | Reason | Date |
|----------|--------|------|
| Order ID = UUID v4 | Agreed with #danny, easy tracking | 2024-01-28 |
| Order status = pending on creation | Pre-payment state | 2024-01-28 |
| Inventory deduction after payment confirmed | No rollback needed on payment failure | 2024-01-28 |

## Coordination with #danny

### Agreed
- Order ID format: UUID v4 (done)
- Payment integration flow:
  1. T-create-order: Create Order (status: pending)
  2. T-payment-intent: Create PaymentIntent (orderId in metadata)
  3. T-webhook-handler: Webhook updates Order status → paid

### Pending
- T-cancel-order (Cancel Order) needs T-webhook-handler (Webhook) complete
- Waiting for Refund logic interface

## Files

```
src/models/order.ts
src/models/orderItem.ts
src/routes/order.ts
src/services/order.service.ts
migrations/20240128_create_orders.ts
```

## Notes

### Order Status Flow
```
pending ──► paid ──► shipped ──► delivered
    │         │
    │         ▼
    │      refunded
    │
    ▼
 cancelled
```

### T-cancel-order Blocked Reason
- Order cancellation requires payment refund
- Refund API needed from Stripe Webhook (T-webhook-handler)
- Unblocks when #danny completes T-webhook-handler
