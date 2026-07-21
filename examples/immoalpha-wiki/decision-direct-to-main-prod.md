---
title: "Decision: straight to main + prod"
type: decision
updated: 2026-07-16
sources: [team convention, confirmed 2026-07-16]
---

# Decision: straight to main + prod

Team convention (clarified 2026-07-16): **everything goes straight to `main`**, prod deploys **without asking**, no mandatory dev smoke tests. No branch/PR requirement for normal work.

Why: single-developer pace; PR-cycle overhead isn't worth it (yet). Exceptions arise case by case (e.g. the parked dedup fix on a branch, [[issue-token-grant-dedup]]).

Constraint for the wiki itself: curator changes still go through a **PR** — knowledge that agents treat as truth needs the human gate.

## Related
[[quirk-gh-actions-deploy-broken]] · [[architecture-overview]]
