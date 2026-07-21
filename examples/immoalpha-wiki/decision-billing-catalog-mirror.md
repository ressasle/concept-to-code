---
title: "Decision: billingCatalog.ts mirrors pricing.ts"
type: decision
updated: 2026-07-16
sources: [memory billing-mock-dev]
---

# Decision: `billingCatalog.ts` mirrors `pricing.ts`

The product catalog is deliberately maintained twice: `pricing.ts` (frontend display) and `billingCatalog.ts` (backend/edge functions). **Every price/product change must touch both files** — otherwise display and billing drift apart.

Drift symptom: price/product in checkout doesn't match the pricing page.

Pointer: `Tools/hot-zone-map/src/` (pricing.ts) · backend catalog next to the billing functions

## Related
[[billing-stripe]]
