---
title: "Decision: .env.development is committed"
type: decision
updated: 2026-07-16
sources: [commit 6f9a034 (2026-07-11), memory julian-local-env-prod]
---

# Decision: `.env.development` is committed

**Why:** Julian hosted main locally with an env pointing at **prod** — so he saw prod bugs while working locally. Since commit `6f9a034` (2026-07-11), `.env.development` is checked in so `npm run dev` runs against dev **everywhere**, with zero setup.

Constraint: the root `.env` (prod + secrets) stays gitignored — only the dev configuration is versioned; it contains no secrets.

## Related
[[quirk-cli-env-targets]] · [[supabase-dev-prod]]
