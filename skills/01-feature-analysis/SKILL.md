---
name: speccen-feature-analysis
description: Phase 01 of speccen. Use when capturing and structuring a deep understanding of a request — an idea, feature, refactoring or fix — based on Architect and user input. No codebase access. No implementation.
---

# speccen — Feature Analysis Skill
## FEATURE ANALYSIS PHASE

> File paths, frontmatter, anchors, and the navigation bar in this skill follow the
> **Bundle & File Naming Conventions** defined in `skills/00-governing-workflow/SKILL.md`.
> Every `phase.md` and `summary.md` carries the navigation bar directly under its H1 title;
> `doc-*.md` and ADR files do not. For this phase,
> `{{PHASE_DIR}}` = `{{FOLDER_NAME}}/phase-01-feature-analysis`, so
> `{{PHASE_FILE}}` = `{{PHASE_DIR}}/phase.md` and `{{SUMMARY_FILE}}` = `{{PHASE_DIR}}/summary.md`.

---

## Purpose

This skill governs the Feature Analysis Phase of speccen. Its sole responsibility
is to capture, structure, and document a deep understanding of **what is being requested**
— the feature, refactoring, or capability under analysis — based entirely on information
provided by the Architect and user.

No codebase access occurs in this phase.
No implementation is proposed in this phase.
No assumptions about the existing code are made in this phase.

---

## Inputs

At the start of Phase 01, the following information has been collected by the
Governing Workflow skill:

- `{{FEATURE_NAME}}` — the name of the request
- `{{FOLDER_NAME}}` — the bundle root, `.speccen/bundles/<id>` (derived at session start)
- Any specifics the user gave: class name, pattern, business capability, service contract,
  domain name, or similar
- The user's description of the request

If any of these inputs are missing or ambiguous, settle them in the interview (Step 2a)
**before writing anything to `{{PHASE_FILE}}`**.

---

## Phase 01 Execution

**Extensions.** Before Step 1, apply the active extensions in `.speccen/extensions/feature-analysis/`
(governing skill → Extensions) and record them in the Extensions table of `{{PHASE_FILE}}`. They
add to or tighten this phase; they never relax it.

### Step 1 — Log the prompt

Append the initiating prompt to `log.md`, verbatim, before doing anything else
(see the Prompt Log section of the governing skill).

### Step 2 — Analyse the request

Before writing, reason carefully about the following:

- What is being asked — in precise technical terms
- What domain or business capability is involved
- What the scope appears to be — what is included and what is explicitly or implicitly excluded
- What terminology the user is using — preserve it exactly, do not substitute your own
- What ambiguities or gaps exist in the description
- How the brief covers the **five essentials** — **kind** (idea, feature, refactoring or fix),
  **problem**, **intended outcome**, **scope** (in and out) and **done when** — and where it is
  missing, vague, too short to tell, or contradicts itself

### Step 2a — Interview the Architect (discovery)

Phase 01 is **discovery**: the Architect may not know yet where this request will land. The
interview makes clear what is known, what is assumed and what is still open — it does not force
decisions.

Run the **`speccen-interview`** skill (`skills/interview/SKILL.md`) — rounds of numbered, clear
questions, each with a proposed answer — over what Step 2 found open. In `gated` mode the
Architect answers; in `autonomous` mode the delegate answers the same questions (see **Mode
behaviour**).

- **The five essentials, only where the brief leaves them open** — missing, vague, too short to
  tell, or contradictory. For a contradiction, quote both statements and propose which holds.
  What the brief states clearly is settled and is not asked again.
- **Questions for the kind of request:**
  - *Feature* — who uses it; the capability and its key rules; constraints; terms.
  - *Refactoring* — the pain behind it; what must stay the same (behaviour, contracts); the
    target structure; what not to touch.
  - *Fix* — current versus expected behaviour; how to reproduce it; impact and severity; where,
    and since when.
  - *Idea* — the value; what would make it worth doing; what it becomes — a feature, a
    refactoring, a fix, or not pursued (then the bundle ends here).
- **"Don't know yet" is a normal answer.** Record the proposed answer as a **working
  hypothesis**, with what would confirm or refute it, and an open item for the phase that
  settles it: Phase 02 for facts in the code, Phase 03 for decisions, Phase 04 for acceptance.
- **Only what blocks Phase 02 must be answered** — for example, which system or area the
  request concerns. Everything else may stay open.
- Questions about the code are marked "for Phase 02" and left out — Phase 01 does not read code.

If nothing is open, skip the rounds. In `gated` mode, close either way with the summary and the
question **"Is this where we stand? — yes / not yet"**, and write nothing in Step 3 before the
"yes".

### Step 3 — Write to the phase file

Write `{{PHASE_FILE}}` with frontmatter (`type: Phase`, `phase: "01 — Feature Analysis"`, `status: IN PROGRESS`, one-line `description`). Emit the common anchors
(`phase-top`, `outcome`, `open-items`) and the phase-specific anchors
(`feature-description`, `hypotheses`, `interview`) at their sections. Use plain `###` subsection headings — do
**not** number them (the old `N.x` numbering was an artifact of the single-document
model and produces orphaned numbers in a per-phase file). Structure:

```markdown
---
type: Phase
phase: "01 — Feature Analysis"
status: IN PROGRESS
description: <one-line summary>
---

<a id="phase-top"></a>
# [FEATURE_NAME] — Feature Analysis

[Index](../index.md) · [State](../STATE.md) · [Log](../log.md)

---

<a id="feature-description"></a>
## Request Description

### Request Summary
### Domain Context
### Scope
### Key Concepts & Terminology

<a id="hypotheses"></a>
## Working Hypotheses

<a id="open-items"></a>
## Open Questions

<a id="interview"></a>
## Interview

<a id="outcome"></a>
## Outcome
```

Populate each subsection:

**Request Summary** — A precise restatement of what has been requested, in your own words.
This is not a copy of the user's prompt — it is your structured understanding of it. Start with
**Kind** (idea, feature, refactoring or fix — `provisional` if not settled yet) and end with
**Done when** (observable; or `open — Phase 04`).

**Domain Context** — The business or technical domain this request belongs to.
What capability it serves. What problem it solves or what improvement it delivers.

**Scope** — What is in scope. What is explicitly out of scope. What is still unclear — as an
open item with the phase that settles it.

**Key Concepts & Terminology** — The domain-specific terms, class names, patterns, or
concepts identified in the user's description. Defined as the user uses them — not as
general programming terms.

**Working Hypotheses** (under the `hypotheses` anchor) — what is assumed for now, each with what
would confirm or refute it and the phase that checks it. Hypotheses are about the request, never
about the code. If there are none, state that explicitly.

**Open Questions** (under the `open-items` anchor) — what is still open, each with the phase that
settles it: Phase 02 (facts in the code), Phase 03 (decisions) or Phase 04 (acceptance). In
`gated` mode this includes the questions answered "later" or left when the interview stopped,
and those marked "for Phase 02". Only a question that blocks Phase 02 may hold the phase. If
there are none, state that explicitly.

**Interview** (under the `interview` anchor) — one table per round, as the `speccen-interview`
skill defines: dependencies, the proposed answer, and who answered.

**Outcome** (under the `outcome` anchor) — A one-to-three sentence statement of what this
phase established: the structured understanding of the request that Phase 02 will build on.

### Step 4 — Write the summary file

Write `{{SUMMARY_FILE}}` (`type: Summary`, `phase: "01 — Feature Analysis"`,
`status: AWAITING ARCHITECT REVIEW`, `phase_file: ./phase.md`, one-line `description`).
Keep it compact — work completed, outcome, and open items only, each linking back into
`{{PHASE_FILE}}`'s anchors:

```markdown
---
type: Summary
phase: "01 — Feature Analysis"
status: AWAITING ARCHITECT REVIEW
phase_file: ./phase.md
description: <one-line outcome>
---

# Phase 01 Summary — Feature Analysis

[Index](../index.md) · [State](../STATE.md) · [Log](../log.md)

---

**Work completed:** [one or two lines]

**Outcome:** [one line] → [full detail](./phase.md#outcome)

**Open items carried forward:** [list, or "none"] → [detail](./phase.md#open-items)
```

### Step 5 — Update workflow state

*These steps are the `gated` hand-off. In `autonomous` mode see **Mode behaviour** below.*

Update `{{FOLDER_NAME}}/STATE.md`:
- `Phase Status` → `AWAITING ARCHITECT REVIEW`
- `Pending Architect Action` → `Review Phase 01 output. Signal readiness for Phase 02.`

### Step 6 — Notify the Architect

Inform the Architect that the Feature Analysis Phase is complete. Do not summarise the phase file
in chat — direct the Architect to read the summary. State clearly what action is expected:
> "Feature Analysis Phase is complete. Please review `phase-01-feature-analysis/summary.md`
> and signal when you are ready to proceed to Code Analysis Phase, or add annotations if
> corrections are needed."

---

## Mode behaviour

**`gated`** — Steps 5 and 6 apply as written: the phase ends with a stop, and the Architect's
signal advances it.

**`autonomous`** — the delegate ends the phase with its gate test, in place of Steps 5 and 6:

- Run the interview yourself (Step 2a): the same numbered questions, answered from the decision
  basis and recorded with who answered. Phase 01 is discovery: an open question that Phase 02 can
  investigate becomes a **working hypothesis** with its target phase — not a stop. Only an open
  question that **blocks Phase 02** (for example, which system or area), and that the basis cannot
  answer, is a stop. A non-material choice is decided and recorded in `delegated-decisions.md`.
- Run the Quality Criteria below item by item with cited evidence.
- Write `## Delegate gate check` into `{{SUMMARY_FILE}}`, then transition (Phase Transition
  Protocol). Send no Step 6 message.
- Decision basis, stop conditions and records: **Autonomous Execution** in the governing skill.

---

## What Phase 01 Does NOT Do

- Does not access the codebase
- Does not propose a solution or approach
- Does not describe how the feature will be implemented
- Does not make assumptions about the existing code structure
- Does not force a decision the Architect cannot make yet — it records a working hypothesis instead
- Does not advance to Phase 02 without the Architect's signal (`gated`) or a passed delegate gate check (`autonomous`)

---

## Quality Criteria

Phase 01 output is complete when:

- [ ] Extensions: every file in `.speccen/extensions/feature-analysis/` is in the Extensions table; every active one was applied, each applicable requirement met or marked "not applicable, because…"
- [ ] The request is restated in precise technical terms
- [ ] The domain context is documented
- [ ] Scope boundaries are explicit — including what is unclear
- [ ] All key terminology from the user is captured and defined
- [ ] The kind of request is recorded (`provisional` is allowed)
- [ ] Known facts, working hypotheses and open items are each recorded; every open item names the phase that settles it
- [ ] No open question blocks Phase 02
- [ ] `gated`: the Architect answered "yes" to "Is this where we stand?"
- [ ] Open questions are listed, or explicitly stated as none
- [ ] `{{PHASE_FILE}}` emits the required anchors
- [ ] `{{SUMMARY_FILE}}` is written and links back to the phase file
- [ ] `gated`: `STATE.md` reflects `AWAITING ARCHITECT REVIEW`
- [ ] `autonomous`: `## Delegate gate check` is written in the summary, with evidence for every item
