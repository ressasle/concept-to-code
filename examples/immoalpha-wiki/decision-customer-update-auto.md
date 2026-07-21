---
title: "Decision: customer_update auto in checkout"
type: decision
updated: 2026-07-16
sources: [commit d0bc2ea]
---

# Decision: `customer_update: {address: 'auto', name: 'auto'}` in checkout

Stripe Tax rejects checkout sessions for an **existing customer without a stored address**. Therefore `create-checkout-session` sets `customer_update: {address: 'auto', name: 'auto'}` so Stripe takes the data from the checkout form.

**Do not remove** — without it, every purchase by an existing customer without an address fails (commit `d0bc2ea`, 2026-07-14).

Pointer: `supabase/functions/create-checkout-session/`

## Related
[[billing-stripe]] · [[issue-token-grant-dedup]]
