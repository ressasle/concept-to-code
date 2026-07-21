# C2C — Concept to Code (v2)

> **One line:** We turn the agent's guesses into human-approved decisions *before* the build loop runs — and v2 makes the Kasona Documentation Blueprint the mandatory format backbone of every doc in that process.
>
> **v2 — 2026-07-21**, merged with the Kasona Documentation Blueprint (Julian & Lowel, Lowel check-in 2026-07-21).

---

## 1. Why this exists

Agentic coding drifts when the agent hits something the brief didn't cover. It *guesses* — a data model, a naming scheme, an edge-case behavior — and keeps going. Every guess is a silent bet, and the wrong ones surface late. That's the failure mode Lowel keeps paying for in debugging time and restarted conversations.

The fix is not a smarter agent — it's making sure the agent **never has to guess**. Three layers do that:

| Layer | Answers | Lives in |
|---|---|---|
| **Format** — the Documentation Blueprint | *What does a doc look like?* Goal · Input→Processing→Output · Out of scope · References — same places in every doc | `TEMPLATES.md` (Blueprint core skeleton, mandatory in every type) |
| **Process** — C2C | *How does a doc come to exist, and what happens with it?* Interview → readiness gate → agent-spec → build loop → write-back | `SKILL.md` + `SKILL-FINALIZE.md` |
| **Memory** — the living knowledge base | *Where does context come from, where do learnings go?* Curated, interlinked markdown articles the agent reads before the first prompt | `KNOWLEDGE-BASE.md` (pattern + conventions); per-project `wiki/` (ImmoAlpha pilot exists: 4 hubs + 19 atoms) |

These aren't competing ideas — the Blueprint is the format layer of C2C, and the KB is the memory both read from and write back to.

## 2. What v2 folded in from the Kasona Blueprint

Everything from the Blueprint's core skeleton is now **mandatory** in every C2C doc:

| Kasona Blueprint | Now in C2C v2 |
|---|---|
| Goal — one sentence, user outcome | Mandatory first section; also inherited *verbatim* into the agent-spec as the anti-drift anchor |
| **Input → Processing → Output** (flag mocks) | Mandatory in concept AND agent-spec — this was C2C v1's biggest gap |
| Out of scope / what NOT to do | Mandatory; also inherited verbatim into the agent-spec |
| References, not duplication | Mandatory — and it's literally the same rule as the KB's "pointers, never copies" |
| Doc types A/B/C/D + module matrix | Adopted. Audit (C) and Handover (D) skeletons taken as-is (+ KB block). New: **type A' — Upgrade** for existing features (the highest-drift case): mandatory *Current state* section tracing today's IPO before speccing the delta |
| P0–P3 prioritized rollout | Standard section in every agent-spec |
| Persona/JTBD table, cost/precision note, one-evolving-file, language pragmatics | Adopted per module matrix / style rules |

## 3. What C2C adds on top of the Blueprint

1. **A readiness gate with measurable criteria.** Not "Draft/In Review" — a 12-point checklist (problem, goal, KB read, IPO traced, out-of-scope named, acceptance criteria, …). Nothing reaches a build agent until a human approves a 🟢 concept. That's the anti-vibe-coding guarantee.
2. **Decision tiers (the granularity matrix).** The Blueprint stores decisions; C2C sorts them: *expensive-if-wrong × no-obvious-answer* → only that quadrant needs human approval (Tier 1). Everything else is a logged default (Tier 2) or the loop's problem (Tier 3). A whole subsystem typically produces ~1 decision you actually make. The cut line is movable (regulated feature ↑, prototype ↓) — the model proposes the tier, the human owns the cut line.
3. **The escape hatch.** The Blueprint prevents drift while *writing* the doc; C2C also prevents it while *building*: if the loop hits an unforeseen decision that matters, it does **not** guess — it stops, writes a `DECISION NEEDED` block into the concept, and waits. A silent wrong bet becomes a caught one.
4. **Two audiences, two files.** `concept.md` in concept language (for the human decision), `agent-spec.md` fully technical (for the machine), `summary.md` for management. Mixing those audiences in one doc is exactly where long AI conversations drift.
5. **Acceptance criteria as the loop's exit condition.** "Done" is a checklist the loop verifies against — implement → verify → fix → repeat until every box passes. The Blueprint has no definition of done.
6. **Write-back.** On finalize, Tier-1 decisions flow back into the knowledge base (as proposals, human-approved). Docs and KB stay alive instead of rotting.
7. **KB-read → gap-scan → grill only the delta.** Every concept starts by reading the project wiki; inherited constraints are taken over, and the interview only asks what the KB doesn't know — so it gets shorter every week.

## 4. Files in this package

| File | What |
|---|---|
| `TEMPLATES.md` | **Single source of truth** for all doc formats: types A/A'/B/C/D, module matrix, tier reference, style rules |
| `SKILL.md` | `/c2c` — idea → living concept.md (interview, KB read, readiness gate) |
| `SKILL-FINALIZE.md` | `/c2c-finalize` — 🟢 concept → agent-spec + summary + open-issues + changelog + KB write-back |
| `KNOWLEDGE-BASE.md` | The memory layer, raw form: Karpathy wiki pattern, article conventions, read/write wiring — startable per project today, team decisions deliberately deferred |
| `KONZEPT.md`, `SPEC.md` | Original product notes (v1, historical — German; kept for provenance, not needed to use the system) |

**Worked example:** available on request — a real ImmoAlpha feature concept on the v2 template (`ImmoAlpha/C2C/grundriss-renovation-map/`), with a genuine KB block, a full IPO trace with ⚠ mock flags, and a tier-tagged decisions table.
**Slides (6-min pitch):** `ImmoAlpha/docs/c2c-slides/` — pipeline + granularity matrix, with speaker script.

## 5. Quickstart

1. Drop `SKILL.md`, `SKILL-FINALIZE.md`, `TEMPLATES.md` into your agent's skill folder (Claude Code: `.claude/skills/c2c/`; works in any markdown-reading agent — that's the point of markdown-as-protocol).
2. `/c2c upgrade` + problem/goal → the skill reads your project wiki (if present) and the code, traces the current state, and asks only what neither answers.
3. Answer the Tier-1 questions as they surface; everything else becomes logged defaults.
4. When the readiness checklist is 🟢: `/c2c-finalize` → agent-spec with P0–P3 + escape-hatch clause.
5. Hand the agent-spec to any build agent. It implements → verifies against the acceptance criteria → fixes → repeats; on an unforeseen Tier-1 fork it stops and asks instead of guessing.

## 6. Proposed adoption path (Techie Talk)

1. **Now, costs nothing:** Kasona keeps using the Blueprint core skeleton — it *is* the v2 format layer. Docs written either way are compatible.
2. **Pilot:** run ONE Kasona feature (ideally an upgrade to something existing — Lowel's pain case) through the full C2C loop: `/c2c upgrade` → gate → agent-spec → build.
3. **In parallel, costs little:** each project starts its own `wiki/` per `KNOWLEDGE-BASE.md` §6 — no team decisions needed. The shared `knowledge/` repo + weekly curator routine come after (deferred decisions listed in `KNOWLEDGE-BASE.md` §7; full draft: `ImmoAlpha/docs/context-engine-skill-draft.md`).
4. Ship/Kill/Extend after ~4 weeks: interviews measurably shorter, `DECISION NEEDED` escalations the KB could have answered → 0, otherwise kill it — no zombie process.
