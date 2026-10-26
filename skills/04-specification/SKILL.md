---
name: speccen-specification
description: the Specification Phase of speccen. Use when producing a detailed, phase-structured Implementation Plan based on completed analysis, assembling the complete specification (intent + requirements/acceptance scenarios), together with forward-looking executive and business stakeholder seeds. No code is written. Requires explicit Architect go signal to advance to implementation.
---

# speccen — Specification Skill
## SPECIFICATION PHASE

> File paths, frontmatter, anchors, and the navigation bar in this skill follow the
> **Bundle & File Naming Conventions** defined in `skills/00-governing-workflow/SKILL.md`.
> Every `phase.md` and `summary.md` carries the navigation bar directly under its H1 title;
> `doc-*.md` and ADR files do not. For this phase,
> `{{PHASE_DIR}}` = `{{FOLDER_NAME}}/phase-04-specification`, so
> `{{PHASE_FILE}}` = `{{PHASE_DIR}}/phase.md` and `{{SUMMARY_FILE}}` = `{{PHASE_DIR}}/summary.md`.

---

## Purpose

This skill governs the Specification Phase of speccen. It
produces a **detailed, phase-structured Implementation Plan** based entirely on the analysis
accumulated in Phases 01–03, together with two **forward-looking stakeholder seeds** — an
executive and a business view of *what the plan will deliver and why*.

The Implementation Plan is the contract between the analysis and the implementation.
It must be precise enough that Phase 05 can be executed without further analysis decisions.

The stakeholder seeds let a business or executive stakeholder review the **intent** before
any code is written, while it is still cheap to change. They mirror the Phase 06 close-out
documents, but are forward-looking (intent) rather than outcome (what was delivered); Phase 06's
documents later reference them to compare what was promised against what was delivered.

No code is written in this phase.
No code is modified in this phase.
Only planning artifacts are written — the plan and the two stakeholder seeds.

---

## Inputs

- The completed Phase 01, 02, and 03 output
- All ADR files under `{{FOLDER_NAME}}/adrs/` and their compliance responses
- The resolved scope, domain understanding, and identified constraints

---

## SPECIFICATION PHASE Execution

**The Implementation Plan is a planning artifact only. No code is written,
no files are modified, and no implementation begins in this phase — regardless
of how the prompt is worded. The plan is complete when the Architect or its
delegate signals go for Phase 05.**

### Step 1 — Log the prompt

Append the Phase 04 initiating prompt to `log.md`, verbatim.

### Step 2 — Check for existing Implementation Plan

Before writing anything, check whether `{{PHASE_FILE}}` already contains an
Implementation Plan.

- If it **already exists**: do not overwrite it. Notify the Architect:
  > "An Implementation Plan already exists. Do you want me to extend it, replace it,
  > or leave it as-is?"
  Wait for explicit instruction before proceeding. In `autonomous` mode the delegate decides on
  the decision basis and records the choice; replacing the plan counts as a material choice.

- If it **does not exist**: proceed to Step 3.

### Step 3 — Review the full analysis

Before writing the Implementation Plan, re-read the Phase 01–03 phase files and all ADRs.
Confirm that:

- The scope is unambiguous
- All ADRs are reflected in the analysis
- No open questions remain unresolved
- The existing code behaviour is fully understood

If any of the above are not satisfied, do not proceed. Flag the gap to the Architect.

### Step 4 — Design the Implementation Plan structure

The Implementation Plan is structured as **numbered phases** — not as tasks, stories,
or time estimates. Each phase represents a logical unit of implementation that can be
built and validated independently.

For each phase, identify:
- What it delivers
- What it depends on (prior phases)
- What validation is needed before the next phase begins

### Step 5 — Write the Implementation Plan

Write `{{PHASE_FILE}}` (`type: Phase`, `phase: "04 — Specification"`,
`status: IN PROGRESS`, one-line `description`). Emit the common anchors (`phase-top`,
`outcome`, `open-items`) and the phase-specific anchor (`implementation-plan`).
Populate the body under `<a id="implementation-plan"></a> ## Implementation Plan`:

#### Proposed Changes Summary

A concise overview of what will change:
- Files to be created
- Files to be modified
- Files to be deleted
- New classes, methods, or patterns introduced
- Existing patterns eliminated or replaced

#### Target-State Execution Flow

Document the **intended** execution flow after the change, as an **ASCII flow diagram** in
the same style as the current-state diagram from Phase 02 (Code Analysis). This is the
to-be counterpart of the as-is diagram: Phase 02 shows the flow that exists, this shows the
flow that will exist once the plan is implemented. A wrong arrow here is caught before any
code is written.

Use the same conventions — boxes for steps, labelled branches for conditionals, arrows for
flow, terminal outcomes — for example:

```
        ┌──────────────────────────┐
        │  StartSubscription(intent)│
        └────────────┬─────────────┘
                     ▼
            ┌─────────────────────┐
            │ caller authenticated?│
            └───┬─────────────┬────┘
             no │             │ yes
                ▼             ▼
      ┌──────────────┐  ┌──────────────────────────┐
      │ reject (401) │  │ create Subscription       │
      └──────────────┘  │ (Type = Created)          │
                        └────────────┬──────────────┘
                                     ▼
                        ┌──────────────────────────┐
                        │ record status trail       │
                        └────────────┬──────────────┘
                                     ▼
                        ┌──────────────────────────┐
                        │ send confirmation + admin │
                        │ notification (2 mails)    │
                        └────────────┬──────────────┘
                                     ▼
                        ┌──────────────────────────┐
                        │ return Accepted (pending  │
                        │ manual activation)        │
                        └───────────────────────────┘
```

Add a one-line note of what changed relative to the current state, and link back to the
Phase 02 diagram for comparison:
[current-state flow](../phase-02-code-analysis/phase.md#code-analysis).

Keep the diagram faithful to the plan: every branch and step must correspond to something
the Implementation Plan actually specifies. Do not reproduce the Phase 02 as-is diagram
here — link to it; this subsection is the to-be view only.

#### Strategy Evaluation (if multiple approaches exist)

Settle any material choice that the analysis and the ADRs leave open with the
**`speccen-interview`** skill (`skills/interview/SKILL.md`) before you write the plan. In
`autonomous` mode the delegate answers what the basis decides and puts the rest to the Architect
as one stop round.

If the analysis identified multiple viable approaches:

| Strategy | Impact | Risk | Complexity | Verdict |
|---|---|---|---|---|
| Strategy 1 | ★★★★★ | Low | Low | ✅ Selected |
| Strategy 2 | ★★★☆☆ | Medium | High | ❌ Not selected |

State the selected approach and the rationale. If Phase 03 ran prototypes, fill this table from
their records and link each one (`../phase-03-compliance-review/prototype-<N>.md`).

#### Implementation Phases

For each phase:

```
#### Phase N: [Phase Title] [status emoji]

**Why:** [what this phase achieves and why it comes at this point — one to three STE sentences] — source: [Phase 02 anchor, ADR id or `log.md` prompt]

##### N.1 [Subtask title]
[Only if non-obvious — **Why:** one line — source]
- [ ] [Precise, actionable checklist item]
- [ ] [Precise, actionable checklist item]
- [ ] ...

##### N.2 Build and Validate
- [ ] Build [project name]
- [ ] Verify no compilation errors
- [ ] Verify no new warnings
- [ ] [Any phase-specific validation]
```

**Checklist items must be:**
- Specific enough to be unambiguously done or not done
- At the level of individual methods, properties, or files
- Traceable to the analysis (each item should be explainable by something in Phase 02/03)

**Each phase states its Why** — one to three STE sentences: what the phase achieves, and why it
comes at this point in the order (what it depends on or enables). Cite a source: a Phase 02
anchor, an ADR id, a prototype record, or a prompt in `log.md`. If no source exists, write `Why: no recorded
rationale` and add it to the `open-items` anchor — never invent a rationale.

**A subtask gets a one-line Why only when it is non-obvious** — a step forced by an ADR or a
CRITICAL convention, an order that looks arbitrary, or a deliberate omission of an alternative.
Routine steps get none. Checklist items never get one.

**Phase structure typically follows:**
1. Create new shared infrastructure (helpers, base classes, result types)
2. Refactor primary component
3. Refactor secondary components (if applicable)
4. Integration testing & validation
5. Documentation & cleanup

#### Risk Mitigation

For each significant risk identified in the analysis:

| Risk | Impact | Mitigation |
|---|---|---|
| [Risk] | High/Medium/Low | [Concrete mitigation step] |

#### Test Plan

Testing is a first-class, planned part of the implementation — decided and approved here, in
Phase 04, and executed in Phase 05 **after** the code is written (speccen does not use TDD; tests
are post-implementation). The plan names *what* will be tested and *how*; Phase 05 writes the
tests against this plan.

Use the project's existing test conventions (framework, layout, naming, mocking) as captured
in Phase 02 — fit the plan to the codebase, do not impose a generic approach.

**Scope — what gets tested, and what does not:**

| Target | Test type | Why |
|---|---|---|
| [component / operation / path] | unit | [the behaviour to verify] |
| [cross-component / boundary flow] | integration | [the interaction to verify] |
| [thing deliberately not tested] | none | [legitimate reason — e.g. config-only change, generated code, spike] |

"None, because …" is a **valid, approved outcome** — forcing tests onto everything breeds
useless tests. But it must be recorded with its reason, never silently omitted.

**Behaviours to cover (derived from the contract/requirement, not from code that does not yet
exist):** list the concrete cases each test must establish — stated as *what must be true*.
Give particular weight to the layers an implementation is most likely to get subtly wrong:
**error and failure behaviour** (which condition produces which failure, and what happens to
state on failure — rollback, partial commit, trail entry), **side effects** (mail, audit log,
cache, events), **boundary and edge cases** (empty, null, malformed, limits), and
**ordering/idempotency** where they matter. Input/output shape alone is not a behaviour worth
a test — the compiler already enforces it.

**Coverage intent:** qualitative by default — critical paths and the edge/error/side-effect
behaviours above are covered. A numeric coverage target is optional; include one only if the
Architect wants it (a percentage is gameable and is not required).

This Test Plan is approved as part of approving the Implementation Plan, by the Architect or its
delegate. In Phase 05 the planned tests must be written and passing before the accept gate (only
the Architect may waive a planned test, recorded as a deviation).

#### Deviations Protocol

State explicitly:
> "Any deviation from this Implementation Plan during Phase 05 must be:
> 1. Stopped immediately
> 2. Documented in `phase-05-implementation/phase.md` under the `deviations` anchor
> 3. Reviewed by the Architect before implementation continues"

Record the selected approach under the `outcome` anchor, and any residual risks or
assumptions under the `open-items` anchor.

### Step 6 — Generate the stakeholder seed documents

Alongside the Implementation Plan, produce two **forward-looking** audience seeds — the
non-technical views of *what the plan will deliver and why*. The Implementation Plan itself
is the technical view, so only the executive and business audiences are added here.

These are **expansion seeds**, identical in form to the Phase 06 seeds: terse bullets under
fixed headings, `expandable: true`, never shared verbatim outside the development team.
The difference is tense — these describe **intent before the build**, not outcome after it.
They are **frozen** once Phase 04 closes: Phase 05 does not edit them, and Phase 06 writes
its own outcome seeds that reference these rather than overwriting them.

Write both documents in **STE-style** (ASD-STE100 — see the STE-style writing convention in
`skills/00-governing-workflow/SKILL.md`): short active sentences, plain consistent
vocabulary, one meaning per word.

**`{{PHASE_DIR}}/doc-executive.md`** — executive/sponsor, forward-looking:

```markdown
---
type: Doc
audience: executive
expandable: true
status: AWAITING ARCHITECT REVIEW
description: <one-line intended outcome>
---

# [FEATURE_NAME] — Business Outcome Brief (intent, seed)

## Capability To Be Delivered
- The business capability this plan will create, in one or two bullets.

## Risk Delta (expected)
- Risk this is expected to close: [...]
- Risk being accepted or deferred (e.g. acknowledged-imperfect model, later hardening): [...]

## What It Will Unblock
- What this enables next — dependent features, go-live, revenue path, etc.

## Scope Note
- TCO / cost / budget: out of scope for this feature-level brief.
```

**`{{PHASE_DIR}}/doc-business.md`** — business stakeholder, forward-looking:

```markdown
---
type: Doc
audience: business
expandable: true
status: AWAITING ARCHITECT REVIEW
description: <one-line intended capability>
---

# [FEATURE_NAME] — Business Process & Experience (intent, seed)

## Capability
- What will be possible that is not possible today — in business terms.

## Business Process (end to end, intended)
- Step-by-step flow of the process this feature will enable, from trigger to outcome.
- Include human-in-the-loop or out-of-band steps where they apply.

## Where It Will Surface (UI/UX)
- The surface(s) through which the process will be exercised (screen, page action, email,
  API, etc.). Populate only what is planned — if there is no user-facing surface, say so
  and describe the non-UI touchpoints.

## What Will Change For The User
- Concrete before/after from the user's or stakeholder's point of view.

## Not Included
- Explicit boundaries — what this feature deliberately will not do (e.g. deferred to a
  later feature).
```

Keep both as brief as the feature warrants — stub a heading with one line if there is
little to say. They exist so a business or executive stakeholder can review and sanity-check
the **intent** while it is still cheap to change, before any code is written.

### Step 7 — Assemble the specification

At the end of Phase 04, the **complete spec** exists — what is being changed (Phase 01),
what already exists (Phase 02), the decisions and constraints (Phase 03 ADRs), and how it
will be designed and built (this phase). It exists, but **distributed** across the phase
files. This step **assembles** it into one artifact: `{{PHASE_DIR}}/specification.html` (the
authored source) and `{{PHASE_DIR}}/specification.md` (a reduced Markdown projection of it).

The specification is the **intent** counterpart to the Phase 08 `reference` (the **outcome**):
the spec is "what we set out to build and how we'll know it's done," assembled *before* code;
the reference is "what we actually built," assembled *after*. The two are a matched pair.

**Assembled, not authored.** The specification is a **projection** that consolidates the
01–04 phase files — never a fifth place the Architect writes content. The phase files remain
the source of truth; the specification is their assembled, readable view, so it cannot drift.
(This is the opposite of spec-driven tools where the spec is the hand-authored source of
truth; in speccen the spec is *earned* from gated analysis.)

**Same machinery as the Phase 08 reference.** Produce it exactly as
`skills/08-reference/SKILL.md` produces its reference: a self-contained,
**depth-layered HTML** page (C4 progressive disclosure, delta-highlighting, argument-diagrams,
verify every diagram renders), in **STE**, with `specification.md` extracted as a reduced
projection. Study the same reference example (`examples/pump-safety-rules-refactor.html`) for
the structure and craft; do not imitate its non-STE prose voice.

**Content — assembled from the phases, plus the requirements section:**

- **What is being built** — from Phase 01: the capability, scope, domain, terminology.
- **What already exists** — from Phase 02: the current-state architecture the change meets
  (the C4 context/containers the reader needs to understand the change).
- **Decisions & constraints** — from Phase 03 ADRs: the settled decisions the design honours.
- **How it will be built** — from this phase: the design, proposed changes, target-state
  execution flow, the implementation approach and each phase's **Why** (summarised — the full plan
  stays in `phase.md`).
- **Requirements & acceptance scenarios** — see below. This is the **verifiable** core.

**Requirements & acceptance scenarios (the verifiable spec).** A dedicated section stating
each requirement and the concrete scenario that proves it — the `SHALL` / `WHEN–THEN` shape
(EARS / OpenSpec style). These are the **acceptance criteria**: they define *done*. The Phase
04 Test Plan tests against them, and Phase 07 review checks them. Format:

```markdown
## Requirements & acceptance scenarios

### Requirement: <short title>
The system SHALL <the required, testable behaviour>.

#### Scenario: <what this proves>
- WHEN <condition / trigger>
- THEN <the observable, checkable outcome>

(repeat per requirement; cover the error/failure behaviour and edges, not only the happy path)
```

Keep requirements grounded in Phase 01's intent and Phase 03's decisions — do not invent
scope. If a requirement cannot be stated testably, that is a gap to flag to the Architect, not
to paper over.

The specification is **cleanly linked**: it carries the `[Index]` nav bar, internal anchors,
and bundle-relative links, the same as every bundle document. At close-out it graduates to the
feature node and cross-links with the reference (see Phase 06 and the Graph model).

### Step 8 — Write the summary file

Write `{{SUMMARY_FILE}}` (`type: Summary`, `phase: "04 — Specification"`,
`status: AWAITING ARCHITECT REVIEW`, `phase_file: ./phase.md`, one-line `description`).
The front-door links the plan, the specification, and both stakeholder seeds:

```markdown
---
type: Summary
phase: "04 — Specification"
status: AWAITING ARCHITECT REVIEW
phase_file: ./phase.md
description: <one-line outcome>
---

# Phase 04 Summary — Specification

[Index](../index.md) · [State](../STATE.md) · [Log](../log.md)

---

**Work completed:** [plan structure; number of implementation phases]

**Outcome:** [selected strategy, one line] → [full detail](./phase.md#implementation-plan)

**Specification (the complete, assembled spec — intent + requirements/scenarios):**
- [specification.html](./specification.html) — full, authoritative (reduced view: [specification.md](./specification.md))

**Stakeholder seeds (intent — expansion seeds, not for verbatim external sharing):**
- Executive → [doc-executive.md](./doc-executive.md)
- Business → [doc-business.md](./doc-business.md)

**Open items carried forward:** [residual risks, or "none"] → [detail](./phase.md#open-items)
```

### Step 9 — Update workflow state

*These steps are the `gated` hand-off. In `autonomous` mode see **Mode behaviour** below.*

Update `{{FOLDER_NAME}}/STATE.md`:
- `Phase Status` → `AWAITING ARCHITECT REVIEW`
- `Pending Architect Action` → `Review Implementation Plan. Add ADR annotations if corrections needed. Signal go for Phase 05 to begin implementation.`

### Step 10 — Notify the Architect

> "Specification Phase is complete. The Implementation Plan is ready for review in
> `phase-04-specification/`, together with forward-looking executive and business
> seeds describing what the plan will deliver. Please review it carefully. Add ADR annotations
> if corrections are needed. When you are satisfied, signal go for Phase 05 to begin implementation."

---

## Mode behaviour

**`gated`** — Steps 9 and 10 apply as written: the Architect reviews the plan and the
specification and signals go for Phase 05.

**`autonomous`** — Steps 1–8 are unchanged. The delegate approves the Implementation Plan and
the Test Plan as part of the gate test (a recorded decision; waiving a test is never delegated)
and replaces Steps 9 and 10 with:

- Run the Quality Criteria below item by item with cited evidence.
- **Traceability check.** Write a table under `## Delegate gate check`: every requirement stated
  in the Architect's prompts (`log.md`) and every ADR, the acceptance scenario(s) that cover it,
  and the Test Plan entries that test those scenarios. A requirement or ADR with no scenario
  fails. A scenario with no test fails. A specification item with no source — Phase 01 intent,
  a Phase 03 decision or an ADR — is invented scope and fails. Each implementation phase's
  **Why** must cite such a source too, or the phase fails.
- **Material choices.** Work through them with the interview. A contract, data-model,
  persistence, security, dependency or plan-structure choice that the basis does not determine
  goes to the Architect in one stop round, with your proposed answer.
- A passed gate test is the go for Phase 05. It freezes the specification and the intent seeds,
  exactly as closing Phase 04 does today. Send no Step 10 message.
- Decision basis, stop conditions and records: **Autonomous Execution** in the governing skill.

---

## What Phase 04 Does NOT Do

- Does not write or modify any code
- Does not begin implementation "just to validate the plan"
- Does not use sprint, timeline, or duration language
- Does not make the Implementation Plan vague to preserve flexibility
- Does not fabricate TCO, cost, or budget figures in the executive seed
- Does not write the stakeholder seeds as polished prose — they are terse, forward-looking seeds
- Does not treat the stakeholder seeds as an approval gate — only the go signal unlocks Phase 05 (the Architect's in `gated` mode, the delegate's passed gate test in `autonomous` mode)
- Does not author the specification independently — it is an assembled projection of the phase files, so it cannot drift
- Does not invent requirements or scope in the specification beyond Phase 01 intent and Phase 03 decisions
- Does not invent a rationale — a Why cites a source, or says none is recorded
- Does not advance to Phase 05 without the Architect's go signal (`gated`) or a passed delegate gate check (`autonomous`)

---

## Quality Criteria

Phase 04 output is complete when:

- [ ] Proposed changes summary lists all affected files with their change type
- [ ] Target-state execution flow is rendered as an ASCII diagram, linking back to the Phase 02 as-is diagram
- [ ] Strategy evaluation is present if multiple approaches were identified
- [ ] Implementation phases are numbered and sequenced logically
- [ ] Every checklist item is specific, actionable, and traceable to the analysis
- [ ] Each phase ends with a Build and Validate step
- [ ] Every implementation phase states a **Why** (one to three sentences) with a source — or `no recorded rationale` is raised as an open item; a subtask carries a Why only where it is non-obvious
- [ ] Risk mitigation table is populated
- [ ] Test Plan is present: unit/integration scope (or "none, because…"), behaviours to cover derived from the contract (error/failure, side effects, edges), qualitative coverage intent
- [ ] `doc-executive.md` and `doc-business.md` (forward-looking intent seeds) are written, `expandable: true`
- [ ] Both stakeholder seeds are written in STE-style (short active sentences, plain consistent vocabulary)
- [ ] Executive seed keeps TCO out of scope (no fabricated figures)
- [ ] Deviations protocol is stated
- [ ] `specification.html` is assembled (projection of the 01–04 phase files, STE, depth-layered) with `specification.md` extracted as its reduced projection
- [ ] The specification includes a **Requirements & acceptance scenarios** section (`SHALL` / `WHEN–THEN`), covering error/edge behaviour, not only the happy path
- [ ] Specification diagrams were verified to render
- [ ] `{{SUMMARY_FILE}}` is written and links to the plan, the specification, and both stakeholder seeds
- [ ] `gated`: `STATE.md` reflects `AWAITING ARCHITECT REVIEW`
- [ ] `autonomous`: `## Delegate gate check` is written in the summary, with evidence for every item
