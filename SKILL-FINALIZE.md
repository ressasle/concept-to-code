---
name: c2c-finalize
description: Finalizes a C2C concept. Fills agent-spec.md and summary.md from a finished concept.md (v2 templates), creates changelog.md + open-issues.md, and writes decisions back into the knowledge base. Activate with /c2c-finalize.
---

# C2C Finalize — v2

> Templates: `TEMPLATES.md` §4 (agent-spec) + §5 (summary). This skill only describes the process.

## When to activate

```
/c2c-finalize [feature-name]
```

Without a feature name: take the only `C2C/` subfolder, or ask which one.

---

## ⛔ CRITICAL RULES

1. **Check the gate:** `concept.md` must exist AND have readiness 🟢 (all criteria ✅, no open Tier-1 questions). Otherwise abort: *"Concept is not 🟢 — open: <list>. Close these via `/c2c` first."* The gate cannot be talked around.
2. **Never change concept.md** — read only (single exception: set `status: "final"` at the end).
3. **File-first** — chat reply max 5-8 lines.
4. **Files in English**, chat in the user's language.

---

## The flow

1. **Load concept.md completely.**
2. **Analyze the codebase** — silently, along the "Affected components".
3. **Fill agent-spec.md** (template §4). Mandatory properties:
   - **Inherit Goal, Out of scope, and the Decisions table verbatim from concept.md** — never paraphrase (paraphrasing is a drift gateway).
   - IPO trace made technically concrete (tables, functions, endpoints; ⚠ mock flags remain).
   - **P0–P3 rollout:** P0 = smallest shippable slice (hours), P1 days, P2 1-2 weeks, P3 expansion.
   - **Escape-hatch clause** (§4 wording): unforeseen Tier-1 decision → STOP, write `## DECISION NEEDED` into concept.md, never guess.
   - Acceptance criteria = the build loop's exit conditions (implement → verify → fix → repeat). `[M]`/`[H]` tags carry over unchanged from concept.md; the loop verifies `[M]` only, `[H]` stay open for human acceptance.
   - Complete enough that an agent can work without follow-up questions: all paths, numbered requirements, edge cases, existing patterns as reference.
4. **Fill summary.md** (template §5) — max 1 page, no jargon, highlight open feedback points.
5. **Create open-issues.md:** all Tier-2 defaults ("set, tunable") + consciously deferred points — so nothing disappears silently.
6. **Create changelog.md** (decision ledger): per Tier-1 decision date · decision · reasoning · trade-off · rejected alternative. The loop appends to it during the build.
7. **Write-back into the KB (NEW):** if the project has a KB (`wiki/` / `knowledge/`): check which articles are affected by the Tier-1 decisions and propose updates (new `decision-*` atom or amendment of existing articles; date contradictions, never overwrite). As a proposal/PR, not a silent commit — the human approves. Without this return channel the KB rots.
8. **concept.md:** set `status: "final"`; update `C2C/overview.md` (the index).
9. **Chat reply** (max 5-8 lines): paths of the filled files, number of Tier-1 decisions, proposed KB updates. Last line: "Next step after the build: /abnahme" (only if the project has that skill; otherwise: "`[H]` criteria need human acceptance").
