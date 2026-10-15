# C2C Templates v2 — Doc Formats

> **Status:** v2 — 2026-07-21
> **What this is:** The single source of truth for every C2C document format. v2 merges the C2C v1 templates with the Kasona Documentation Blueprint (Julian & Lowel, 2026-07-21): the Blueprint's core skeleton (Goal · Input→Processing→Output · Out of scope · References) is now the mandatory backbone of every doc type, and C2C adds the process layer on top (readiness gate, decision tiers, escape hatch, write-back).
> **Used by:** `SKILL.md` (/c2c) and `SKILL-FINALIZE.md` (/c2c-finalize). The skills reference these templates — they do not duplicate them.

---

## 0. Pick your doc type

| Type | When to use | Runs through the C2C gate? |
|---|---|---|
| **A — Feature** (`/c2c feature`) | Speccing a NEW feature before building it | ✅ concept → gate → agent-spec → loop |
| **A' — Upgrade** (`/c2c upgrade`) | Changing a feature that ALREADY EXISTS (the highest-drift case) | ✅ same, plus mandatory Current-State section |
| **B — Concept / Proposal** (`/c2c concept`) | A design decision must be made before anything gets built (data model, architecture) | ✅ gate = the decision itself; may never reach agent-spec |
| **C — Audit** | Reviewing a shipped module that "feels off" | ❌ format-only (no build loop); output often becomes a Type A' input |
| **D — Assessment / Handover** | Briefing one specific person on a problem they'll own | ❌ format-only |

**Core skeleton — required in every type, in this order where present:**
1. **Goal** — one plain-language sentence, user outcome, not implementation
2. **Input → Processing → Output** — explicit data-path trace, mocks flagged
3. **Out of scope / what NOT to do** — explicit anti-goals
4. **References** — link, don't duplicate (personas, docs, code paths, KB articles)

Build docs (A/A'/B) additionally require: **Context from the KB**, **Decisions (numbered, tiered, binding)**, **Acceptance criteria**, and the **Readiness checklist**.

---

## 1. Optional-module matrix

| Module | A: Feature | A': Upgrade | B: Concept | C: Audit | D: Handover |
|---|:---:|:---:|:---:|:---:|:---:|
| Executive summary (plain, priority order) | – | – | ✅ | ✅ | ✅ |
| Persona lens (persona → need → current state, quoting JTBD) | ✅ | optional | optional | ✅ | optional |
| Problem statement | ✅ | ✅ | ✅ | ✅ | ✅ |
| Current state (IPO of today, mocks flagged) | – | ✅ | optional | ✅ | ✅ |
| Data model / schema | optional | optional | ✅ | optional | optional |
| Cross-feature integration | optional | ✅ | ✅ | ✅ | optional |
| Prioritized rollout P0 (hours) / P1 (days) / P2 (1–2 wks) / P3 (expansion) | ✅ | ✅ | ✅ | ✅ | – |
| Cost/precision discipline note (SQL free, LLM expensive — spend once per entity, not per user; precision over recall) | optional | optional | ✅ | ✅ | – |
| Open questions / explicit ask to recipient | ✅ | ✅ | ✅ | – | ✅ |

Rule of thumb (from the Kasona Blueprint, unchanged): the more a doc asks someone to **build now**, the more P0–P3 + Decisions apply; the more it asks someone to **review/decide**, the more Problem statement + Data model apply.

---

## 2. Decision tiers (reference)

Every decision recorded in a build doc gets a tier. Sort by two questions: *how expensive is a wrong guess* × *is there an obvious right answer*.

| | Obvious answer | No obvious answer |
|---|---|---|
| **Expensive if wrong** | **Tier 2 — INFORM**: agent decides, logs reasoning | **Tier 1 — APPROVE**: human gate, blocks |
| **Cheap if wrong** | **Tier 3 — DEFER**: loop decides | **Tier 2 — INFORM**: sensible default, flagged for tuning |

- Only Tier 1 blocks. Approval load scales with consequence, not feature size.
- The cut line is **movable**: regulated/money-touching → more Tier 1; prototype → more Tier 3. The model proposes the tier; the human owns the cut line.
- **Escape hatch:** if the build loop hits a decision that matters and was not pre-approved, it does NOT guess — it stops, writes a `DECISION NEEDED` block into the concept doc, and waits.

---

## 3. Template — `concept.md` (types A / A' / B)

````markdown
---
name: "<feature-name>"
type: "feature | upgrade | concept"
status: "draft"
created: "YYYY-MM-DD"
readiness: "🔴 | 🟡 | 🟢"
target_project: "<path or repo>"
---

# <Type>: <Name> — <YYYY-MM-DD> (vN)

Status: Draft | In Review | Decided | Ready to build

## Readiness: 🔴/🟡/🟢

| Criterion | Status |
|---|---|
| Problem described | ✅/❌ |
| Goal stated (one sentence, user outcome) | ✅/❌ |
| KB read + gap scan done | ✅/❌ |
| Codebase analyzed | ✅/❌ |
| Current state traced (A' only: IPO of today) | ✅/❌/n.a. |
| Input → Processing → Output traced (target) | ✅/❌ |
| Approach formulated | ✅/❌ |
| Components identified | ✅/❌ |
| Edge cases defined | ✅/❌ |
| Out of scope named | ✅/❌ |
| Open questions resolved | ✅/❌ (N open) |
| Acceptance criteria formulated and tagged `[M]` or `[H]` | ✅/❌ |

## Goal

<ONE sentence, plain language, stated as the outcome for the user — not the
implementation. Example: "Organize the user's portfolio assets to maximize
return on equity for their chosen strategy" — not "build a table with columns X, Y, Z.">

## Context from the KB

Read: [[article-1]], [[article-2]]
Constraints inherited: <taken over from the KB, not re-invented>
Gap: <what the KB does NOT answer → these become the interview questions>

## Problem

<Why does this need to exist / change at all — in everyday language.>

## Current state — only for type A' (upgrade)

<What exists today. Trace the CURRENT data path so the delta is explicit:>

| Input (today) | Processing (today) | Output (today) | Mocked/hardcoded? |
|---|---|---|---|
| <source> | <deterministic calc / LLM call> | <what the user sees> | <⚠ flag> |

What stays untouched: <explicit — this is drift protection for existing behavior>

## Input → Processing → Output (target)

For every distinct data path this doc covers:

| Input | Processing | Output |
|---|---|---|
| <where does the data come from — user input, live API, cached table, mock (⚠ flag)> | <deterministic calc or LLM call? If LLM: exact prompt/context, and is that context enough for a non-generic answer?> | <what the user actually sees — the decision-relevant version or just a raw number?> |

## User's notes

<Context, preferences, constraints the user gave verbatim.>

## Assessment

| Dimension | Rating | Reasoning |
|---|---|---|
| Workload | 🟢/🟡/🔴 | <components, layers> |
| Scope | 🟢/🟡/🔴 | <blast radius, side effects> |
| Success probability | 🟢/🟡/🔴 | <does the architecture support it?> |

### Risks
- ⚠️ <risk>

## Affected components

### <Component> (lives in: <file/path as orientation>)
- **Current behavior:** <what it does now>
- **Planned behavior:** <what it should do instead>
- **Why:** <reasoning>

## Approach

<Conceptual description — WHAT changes and WHY. No code. Readable by a
non-technical person. Concept language: components, behavior, responsibilities.>

## Persona fit (per module matrix)

| Persona | Need (JTBD) | Current state |
|---|---|---|

## Edge cases & failure behavior

| What happens when… | Expected behavior |
|---|---|

## Out of scope / what NOT to do

<Explicit anti-goals. The single highest-leverage section against drift —
name what should NOT be built, not just what should.>

- ❌ <anti-goal>

## ❓ Open questions

1. ❓ <question — and why it matters>

## ✅ Decisions

> Numbered, binding, tier-tagged. Tier 1 = was human-approved. The build
> loop must obey these verbatim; an unforeseen Tier-1 fork triggers the
> escape hatch (stop + DECISION NEEDED, never guess).

| # | Decision | Tier | Why |
|---|---|---|---|
| 1 | <what was decided> | 1/2/3 | <reasoning / trade-off> |

## Acceptance criteria

> "Done" means: every box checked, verified by the loop, not asserted.

- [ ] `[M]` <observable behavior a test can prove, e.g. "tsc and unit tests pass; `GET /x` returns 200 with field `y`">
- [ ] `[H]` <behavior only a human can check at the real channel, e.g. "WhatsApp: sending a photo to the number returns the expanded reply within 30 s">

> Each criterion is exactly one kind: `[M]` machine (the loop verifies it) or `[H]` human (acceptance at the real channel: phone, inbox, browser). `[H]` only if no test can prove it.

## References

<Links only — personas, related docs, KB articles, code paths. Point, don't repeat.>
````

---

## 4. Template — `agent-spec.md` (filled by /c2c-finalize)

````markdown
---
name: "<feature-name>"
status: "ready-for-implementation"
created: "YYYY-MM-DD"
source: "./concept.md"
---

# Agent-Spec: <Name>

> Optimized for an AI implementation agent. Everything needed to implement
> the full scope without guessing. Basis: concept.md of <date>.

## Goal (inherited verbatim from concept.md)

<the one goal sentence — the anchor against drift>

## Mission

<Clear, unambiguous description of what to build — 2–3 sentences.>

## Context

<Existing system: architecture, tech stack, patterns to follow, KB articles read.>

## Input → Processing → Output (target, technical)

<The concept's IPO table, now with concrete tables/functions/endpoints. Mocks still flagged ⚠.>

## Affected files & components

### <file>
- **Path:** `<full path>`
- **Current role:** <what it does>
- **Changes:** <what exactly changes>
- **Relevant existing code:**
```<lang>
<snippets the agent needs as context>
```

## Requirements

1. <numbered requirement>

## Technical details

<Interfaces, data structures, API contracts to honor.>

## Prioritized rollout

- **P0 (hours):** <smallest shippable slice>
- **P1 (days):** <…>
- **P2 (1–2 wks):** <…>
- **P3 (expansion):** <…>

## Edge cases

| Scenario | Expected behavior |
|---|---|

## Constraints / out of scope (inherited verbatim from concept.md)

- <what must NOT be changed / built>

## Decisions (inherited verbatim from concept.md, with tiers)

| # | Decision | Tier | Why |
|---|---|---|---|

## ⛔ Escape hatch (binding for the implementing agent)

If you hit a decision that (a) is expensive to get wrong AND (b) has no
obvious answer AND (c) is not covered by the Decisions table above:
**STOP. Do not guess.** Append a `## DECISION NEEDED` block to concept.md
(question, options, your recommendation, what it blocks) and halt that
work stream until a human answers.

## Acceptance criteria (= the loop's exit conditions)

- [ ] `[M]` <measurable criterion — implement → verify against this → fix → repeat until all pass>
- [ ] `[H]` <criterion only a human can check at the real channel, e.g. "mail arrives in the inbox with the PDF attached"; the loop does not tick it>

> Exactly one kind per criterion: `[M]` machine, `[H]` human (acceptance at the real channel). `[H]` only if no test can prove it. The loop exits when all `[M]` pass; `[H]` stay open for acceptance after the build.
````

---

## 5. Template — `summary.md` (management summary)

````markdown
# <Name>

**Status:** Ready to build | Draft
**Estimated effort:** Low/Medium/High
**Affected areas:** <e.g. "analysis view, backend processing">

## What is this about?

<2–3 sentences, no jargon: problem + what we want to achieve.>

## What changes for the user?

- <concrete change>

## Risks

<1–2 sentences.>

## Feedback wanted on:

- <point>

## Next steps

<What happens next, who does what.>
````

---

## 6. Template — Type C: Audit (format-only, Kasona Blueprint skeleton + KB block)

````markdown
# External Audit: <Module> — <YYYY-MM-DD>

## Executive summary
<Root causes / verdict, stated plainly, priority order.>

## Goal
<One sentence: what this module is supposed to achieve for the user.>

## Context from the KB
Read: [[…]] · Constraints inherited: <…> · Gap: <…>

## Audit from the user's perspective (persona lens)
| Persona | Need (JTBD) | Current state |
|---|---|---|

## Current state — Input → Processing → Output
<Trace today's data path; flag everything mocked/hardcoded ⚠ — this is
where audits usually find the real problem.>

## Redesign (target Input → Processing → Output)

## Cross-feature integration

## Prioritized changes (P0–P3)

## Out of scope / what NOT to do

## Decisions (binding for the implementer)
| # | Decision | Tier | Why |
|---|---|---|---|

## References
````

---

## 7. Template — Type D: Assessment / Handover (format-only, Kasona Blueprint skeleton + KB block)

````markdown
# Background: <Topic> — for <Recipient>

## What we are solving

## Goal
<One sentence, user outcome.>

## Context from the KB
Read: [[…]] · Gap: <…>

## What we have already built
<Current state — Input → Processing → Output of today, mocks flagged ⚠.>

## The missing piece / your challenge
<The explicit ask to the recipient — what decision or work is being handed over.>

## Out of scope
<What the recipient should NOT touch or solve.>

## Open questions handed to you
1. <question>

## References
````

---

## 8. Style rules (all types)

1. **One evolving file.** No `_v2` / `_final` filenames — version via the header status line. concept.md is a living document until the gate.
2. **Language:** docs in **English** (agents parse it best, whole team reads it). Chat with the user may be German. Quick internal notes between team members may stay German — don't translate for translation's sake.
3. **Input → Processing → Output is non-negotiable** in every type — it's the section AI implementers reference most. Table or short diagram, not prose.
4. **Name the anti-goals.** Over-building past the stated goal is the second most common drift failure after a missing goal statement.
5. **References, not duplication** — same rule as the knowledge base ("pointers, never copies"). If it lives elsewhere, link it.
6. **Concept language vs. spec language:** concept.md/summary.md carry no code, no line numbers ("the analyzer should detect rooms dynamically", not "replace `rooms` in analyzer.ts:47"). agent-spec.md is the opposite: paths, snippets, interfaces — everything technical lives there.
7. **Decisions carry their why.** A decision without reasoning cannot be re-evaluated later and will be re-litigated.
