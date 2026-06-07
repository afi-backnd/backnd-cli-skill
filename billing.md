# Billing Domain

Commands: `receipt`, `refund`, `cash`, `coupon`, `webshop`, `payment`

## Non-obvious distinctions

**receipt vs payment**
- `receipt` — individual purchase records (per-transaction, per-player)
- `payment` — billing/subscription management for the Backnd service itself (not player purchases)
- "유저 결제 내역" → `receipt`, not `payment`

**cash**
- In-game currency and virtual items
- "재화 지급", "코인 추가" → `cash`
- Not related to real-money transactions

**refund**
- Changes refund status on a purchase. Does NOT trigger a real-money refund by itself.
- Refund processing is always 2 steps — skip either and you leave the record in an inconsistent state.

## Refund 2-step process (critical)

Never change refund status without verifying the receipt first:

```
# Step 1 — find and confirm the receipt
backnd receipt list --json --id <gamer-id>
backnd receipt info --json --receipt-id <receipt-id>

# Step 2 — update refund status
backnd refund --json --receipt-id <receipt-id> --status <status>
```

Ask the user for the receipt ID or order ID before starting. Changing refund status
on the wrong receipt cannot be undone from the CLI.

## Coupon notes

- `coupon` supports `create`, `list`, `usage`, `revoke`
- `coupon usage` returns per-coupon redemption counts — useful for campaign analytics
- Bulk revoke is high-risk (see SKILL.md high-risk section)
