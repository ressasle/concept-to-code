---
title: "Open: token-grant dedup fix waiting for prod deploy"
type: issue
status: open
updated: 2026-07-16
sources: [branch objective-kilby, commit 11c8461]
---

# Open: token-grant dedup fix waiting for prod deploy

Token grants could be **counted twice**. The fix exists (branch `objective-kilby`, commit `11c8461`) but is **not yet deployed to prod** — waiting for release approval.

Until then: for wrong token balances look here first before searching elsewhere. After the deploy: set this article to `status: resolved` (don't delete — history).

## Related
[[billing-stripe]] · [[issue-billing-cron-missing]]
