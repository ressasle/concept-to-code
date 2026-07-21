---
title: "Quirk: which tool points at which Supabase project"
type: quirk
updated: 2026-07-16
sources: [memory supabase-projects]
---

# Quirk: which tool points where

dev = `sampdqsehtqtmpkwwlou` · prod = `adygxbossivdehtwezfa`

| Tool | Points at | Watch out |
|---|---|---|
| Supabase CLI (linked) | **prod** | dev deploys need explicit `--project-ref sampdqsehtqtmpkwwlou` |
| Supabase MCP | **prod** | verify with `get_project_url` when in doubt |
| Root `.env` (gitignored) | **prod** | contains the secrets |
| `.env.development` (committed) | **dev** | this is what `npm run dev` uses |
| dev SQL | Management API | no direct CLI path |

Most common mistake: deploying an edge function "for dev" and landing on prod because `--project-ref` was missing.

## Related
[[supabase-dev-prod]] · [[decision-env-development-versioned]]
