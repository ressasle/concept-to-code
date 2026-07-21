---
title: "Quirk: BILLING_MOCK on dev"
type: quirk
updated: 2026-07-16
sources: [memory billing-mock-dev]
---

# Quirk: `BILLING_MOCK=true` on dev

Dev has **no real Stripe**. `BILLING_MOCK=true` simulates purchases end to end (checkout, grants) without any Stripe round-trip. The Stripe trio + `grant-subscription-tokens` only run for real on prod.

Consequences:
- "Purchase doesn't work on dev" → first check whether BILLING_MOCK is set.
- Real Stripe bugs (Tax, webhooks) are **not reproducible on dev** — that needs prod or Stripe test mode against prod functions.

## Related
[[billing-stripe]] · [[supabase-dev-prod]]
