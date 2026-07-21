---
title: "Quirk: prod still signs HS256 (empty JWKS)"
type: quirk
updated: 2026-07-16
sources: [devlog 2026-07-11, memory prod-jwt-hs256-not-migrated]
---

# Quirk: prod still signs HS256

Prod is **not migrated to ES256** — its JWKS is empty, JWTs are HS256-signed. Dev uses ES256. That's why `requireAuth` in `supabase/functions/_shared/jwt/verify.ts` needs the **`auth.getUser` fallback**: without it, **all** AI functions return 401 on prod while dev works fine.

Fixed + deployed 2026-07-11. **When refactoring `verify.ts`, do not rationalize the fallback away** — it looks redundant but isn't, as long as prod isn't migrated. After a real ES256 migration: set this article to resolved.

Symptom: 401 on edge functions **on prod only** → almost always this, not the client session.

## Related
[[supabase-dev-prod]] · [[pipeline-expose-analysis]]
