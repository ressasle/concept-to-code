---
title: Supabase dev/prod (Hub)
type: hub
updated: 2026-07-16
sources: [memory supabase-projects, devlogs 2026-07-11..15]
---

# Supabase dev/prod

**Core:** Two separate Supabase projects: **dev = `sampdqsehtqtmpkwwlou`**, **prod = `adygxbossivdehtwezfa`**. Almost every environment bug comes from a tool pointing at the other project than assumed.

## Atoms in this domain
- [[quirk-cli-env-targets]] — which tool points at which project (CLI/MCP/envs)
- [[decision-env-development-versioned]] — why `.env.development` is committed
- [[decision-migrations-via-mcp]] — prod migrations via `apply_migration`, never `db push`
- [[quirk-prod-jwt-hs256]] — prod still HS256 → `auth.getUser` fallback in verify.ts is mandatory
- [[quirk-newsletter-prod-only]] — newsletter content exists only on prod
- [[reference-preview-test-account]] — dev test login for browser verification

## Debugging entry points
"Works locally, not on prod" → first [[quirk-cli-env-targets]] (where does the process actually point?). 401 on prod only → [[quirk-prod-jwt-hs256]].

## Related
[[architecture-overview]] · [[billing-stripe]]
