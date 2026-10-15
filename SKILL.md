---
name: c2c
description: Guides the user from an idea (new feature OR upgrade of an existing one) to an implementation-ready specification. Creates a concept.md as a living working document following the v2 templates (Kasona Blueprint skeleton + C2C process). Activate with /c2c.
---

# Concept to Code (c2c) — v2

> Templates: all file formats live in **`TEMPLATES.md`** (single source of truth).
> This skill only describes the process. v2 changes: doc types (feature/upgrade/concept),
> mandatory blocks Goal · KB context · Input→Processing→Output · Out of scope,
> tier-tagged decisions, docs in English.

## When to activate

When the user types `/c2c`:

```
/c2c [feature|upgrade|concept]
Problem: [free text]
Goal: [free text]
Notes: [free text or empty]
```

**Type selection (first action if not given):**
- `feature` — build something new
- `upgrade` — change a feature that ALREADY EXISTS (→ mandatory "Current state" block)
- `concept` — prepare a design decision (data model, architecture)

If unclear: infer from the problem text (does a first version already exist? → `upgrade`) and state the choice in half a sentence in chat. Don't hold a long back-and-forth about it.

---

## ⛔ CRITICAL RULES

### 1. NO CODE
- ❌ Do NOT write or change code. Do NOT modify source files.
- ❌ Do NOT run commands (except for reading/analyzing the codebase).
- ✅ ONLY read codebase + KB. ONLY create/update the concept files.

### 2. CONCEPT LANGUAGE
`concept.md` and `summary.md` stay at concept level: components, behavior, responsibilities — no code snippets, variable names, or line numbers. (Technical detail belongs in `agent-spec.md`, see `/c2c-finalize`.)

### 3. FILE LANGUAGE
All doc files in **English** (team convention: agents parse it best, the whole team reads along). Chat with the user in the user's language.

### 4. FILE-FIRST
Do NOT present content in chat. Everything goes into the files. Chat replies max 5-8 lines: file path, readiness, top open questions (max 3).

### 5. FOLDER STRUCTURE
```
[project root]/
└── C2C/
    └── [feature-name]/           ← kebab-case
        ├── concept.md            ← living concept file (template: TEMPLATES.md §3)
        ├── agent-spec.md         ← placeholder until /c2c-finalize
        └── summary.md            ← placeholder until /c2c-finalize
```

---

## The flow

### Step 1: On `/c2c` invocation

1. **Parse input** — type, problem, goal, notes.
2. **Derive feature name** — kebab-case.
3. **RESUME check:** does `C2C/[feature-name]/concept.md` exist? → load it, say "Found existing concept, continuing on it", go to Step 2. Otherwise create the folder.
4. **KB read (NEW — before the codebase):** if the project has a knowledge base (`wiki/` or `knowledge/projects/<project>/`): read `index.md`, follow relevant articles. Result goes into the **"Context from the KB"** block:
   - *Read:* which articles
   - *Constraints inherited:* constraints taken over (never re-invent them!)
   - *Gap:* what the KB does NOT answer → **only these gaps become interview questions**
5. **Analyze the codebase** — silently in the background. For type `upgrade` additionally: trace the **Current state** (today's Input→Processing→Output, flag mocks/hardcodes with ⚠, state explicitly "what stays untouched").
6. **Create concept.md from the template** (TEMPLATES.md §3) — fully filled with everything KB + codebase yield. Mandatory blocks always: Goal (ONE sentence, user outcome), KB context, IPO (target), Out of scope, Decisions table (tier-tagged proposals), Acceptance criteria, References. agent-spec.md + summary.md as placeholders.
7. **Short chat reply** (max 5-8 lines): path, readiness, open questions (max 3).

### Step 2: Iteration (every further prompt)

1. **Update** concept.md (edit, don't recreate), keep the readiness checklist current.
2. Answered questions move from "Open questions" into the **Decisions table** — numbered, with tier and why. Tier logic: expensive-if-wrong × no-obvious-answer → Tier 1 (human must decide); details in TEMPLATES.md §2. **The model proposes the tier, the user owns the cut line** (may promote/demote any decision).
3. Short chat confirmation (1-3 lines).

### Step 3: Finalization

User says "concept done" / "finalize":
- Readiness must be 🟢 (all criteria ✅, no open Tier-1 questions). If not: name the gaps — **the gate is the point of the system, non-negotiable.**
- Then point to **`/c2c-finalize`**. This skill does NOT fill agent-spec.md/summary.md itself.

---

## Behavior

- **User's language** in chat, **English** in the files.
- **Chat = short.** All substance goes into the files.
- **concept.md is updated after EVERY prompt** in which the user provides something.
- **Acceptance criteria:** when drafting them, ask which ones only a human can check at the real channel (phone, inbox, browser) and tag those `[H]`, the rest `[M]` (TEMPLATES.md).
- **Probe only on critical gaps** (Tier-1 candidates, missing anti-goals) — not on trivia. If the user says "enough", it's enough (the decision gets logged as a Tier-2 default instead of asked).
- Scope change → repeat KB + codebase analysis, fully update concept.md.
- Assessment rules (workload/scope/success probability 🟢🟡🔴): 🟢 = 1-2 components / isolated / patterns exist · 🟡 = 3-5 components / moderate side effects / uncertainties · 🔴 = 6+ components / core architecture / fundamental restructuring.
