---
title: Billing & Stripe (Hub)
type: hub
updated: 2026-07-16
sources: [memory billing-mock-dev, commit d0bc2ea, devlogs 2026-07-14..15]
---

# Billing & Stripe

**Core:** Payment model = subscription + token quotas. Prod runs the Stripe trio (`create-checkout-session`, `create-portal-session`, `stripe-webhook`) plus `grant-subscription-tokens`; quota via `check-quota`/`reset-quota`. On dev, Stripe is fully mocked.

## Atoms in this domain
- [[decision-customer-update-auto]] — Stripe Tax requires `customer_update: auto` in checkout (do not remove)
- [[decision-billing-catalog-mirror]] — `billingCatalog.ts` ↔ `pricing.ts`: always change both
- [[quirk-billing-mock-dev]] — dev has no real Stripe (`BILLING_MOCK=true`)
- [[flow-signup-token-grant]] — free grant runs idempotently through submit-waitlist
- [[issue-token-grant-dedup]] ⚠ open — dedup fix waiting for prod deploy
- [[issue-billing-cron-missing]] ⚠ open — no periodic grant/reset cron

## Debugging entry points
Wrong token balances → check the two open issues first, then the grant path in `stripe-webhook`. Checkout failures for existing customers → [[decision-customer-update-auto]].

## Related
[[supabase-dev-prod]] · [[architecture-overview]]
