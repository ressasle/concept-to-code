---
title: "Decision: PropertyDetails split into buy&hold and fix&flip views"
type: decision
updated: 2026-07-16
sources: [memory propertydetails-bh-flip-split]
---

# Decision: PropertyDetails BH/flip split

PropertyDetails has **two parallel views** — fix&flip and buy&hold — dispatched via **`investment_mode`**. This is deliberate: the two investment logics (exit calculation vs. cashflow/debt service) share too little to keep one combined view clean.

**Do not re-merge** — the seemingly obvious deduplication refactor was considered and rejected; whoever "discovers" it is proposing an already-declined idea.

Pointer: `Tools/hot-zone-map/src/pages/` (PropertyDetails area)

## Related
[[pipeline-expose-analysis]] · [[reference-preview-test-account]]
