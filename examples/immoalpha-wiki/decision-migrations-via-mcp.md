---
title: "Decision: prod migrations via MCP apply_migration, never db push"
type: decision
updated: 2026-07-16
sources: [devlog 2026-07-15, memory prod-pipeline-mai-stand]
---

# Decision: prod migrations via MCP `apply_migration`, never `db push`

`supabase db push` caused **version drift** between migration history and the prod schema in May (cleanup took until 2026-07-15). Since then: prod schema changes go through MCP `apply_migration` (versions the migration cleanly); migration files live in root `supabase/migrations/`.

Typing `db push` against prod repeats the May mistake.

## Related
[[supabase-dev-prod]]
