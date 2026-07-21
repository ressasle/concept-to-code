---
title: "Open: no cron for billing grants"
type: issue
status: open
updated: 2026-07-16
sources: [memory billing-mock-dev]
---

# Open: no cron for billing grants

`grant-subscription-tokens` is currently invoked **only event-driven** (Stripe webhook). A periodic cron run (monthly renewals / reset cycles) **does not exist yet**.

Consequence: missed/failed webhooks are never retried — grants can stay missing until someone follows up manually.

## Related
[[billing-stripe]] · [[issue-token-grant-dedup]]
