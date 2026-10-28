---
name: speccen-close-out
description: the Close-out Phase of speccen. Use after an implementation is accepted (by the Architect, or by its delegate in autonomous mode), to produce three audience-targeted documents (executive, business, technical) as expansion seeds, plus deviations, learnings, and results. No further code changes.
---

# speccen — Close-out Skill
## CLOSE-OUT PHASE

> File paths, frontmatter, anchors, and the navigation bar in this skill follow the
> **Bundle & File Naming Conventions** defined in `skills/00-governing-workflow/SKILL.md`.
> Every `phase.md` and `summary.md` carries the navigation bar directly under its H1 title;
> `doc-*.md` and ADR files do not. For this phase,
> `{{PHASE_DIR}}` = `{{FOLDER_NAME}}/phase-06-close-out`, so
> `{{PHASE_FILE}}` = `{{PHASE_DIR}}/phase.md` and `{{SUMMARY_FILE}}` = `{{PHASE_DIR}}/summary.md`.

---

## Purpose

This skill governs the Close-out Phase of speccen. It is
entered **only after the Architect — or, in `autonomous` mode, its delegate — accepts the
implementation** produced in Phase 05.

Its responsibility is to close out the feature by producing **three audience-targeted
documents**, one for each of the typical audiences of a software project:

| File | Audience | Substance |
|---|---|---|
| `doc-executive.md` | Executive / sponsor | Business-outcome brief — capability delivered, risk delta, what it unblocks. **TCO is out of scope.** |
| `doc-business.md` | Business stakeholder | The end-to-end business process the feature enables; UI/UX described as *where that process surfaces*. |
| `doc-technical.md` | Development team | The technical record — files changed, deviations, learnings, measured results. |

All three are produced for **every** feature (uniformity), however briefly. They are
**expansion seeds**, not finished prose:

- They are **never shared verbatim** outside the development team. Every external reader
  receives a redacted and/or expanded version, generated from the seed in a later pass.
- Because of that, each seed is written as **terse, scannable bullets under fixed
  headings** — clean input for an LLM expansion/redaction pass — not polished narrative.
- Each carries `expandable: true` in frontmatter to mark it as seed material.
- Each is written in **STE-style** (ASD-STE100 — see the STE-style writing convention in
  `skills/00-governing-workflow/SKILL.md`): short active sentences, plain consistent
  vocabulary, one meaning per word.

A feature with little to say to a given audience still gets that audience's file — as a
one-line-per-heading stub. An empty-looking executive brief is a valid, expandable seed;
a missing file is not.

No code is written or modified in this phase.

---

## Inputs

- The accepted implementation recorded in `phase-05-implementation/phase.md`
- The Implementation Plan in `phase-04-specification/phase.md`
- All deviations recorded under `phase-05-implementation/phase.md#deviations`
- The Feature Analysis (`phase-01-feature-analysis/phase.md`) — the source of the
  **business process and purpose** that `doc-executive.md` and `doc-business.md` draw on
- Access to the codebase (read-only) for confirming what changed

---

## CLOSE-OUT PHASE Execution

The **deliverables** of this phase are the three audience documents (`doc-executive.md`,
`doc-business.md`, `doc-technical.md`). `{{PHASE_FILE}}` (`phase.md`) holds only thin
working notes for this phase — initialise it with frontmatter (`type: Phase`,
`phase: "06 — Close-out"`, `status: IN PROGRESS`, one-line `description`) and emit the
common anchors (`phase-top`, `outcome`, `open-items`) plus the phase-specific anchor
`documentation`. The substantive content lives in the three `doc-*.md` files, not here.

**Extensions.** Before Step 1, apply the active extensions in `.speccen/extensions/close-out/`
(governing skill → Extensions) and record them in the Extensions table of `{{PHASE_FILE}}`. They
add to or tighten this phase; they never relax it.

### Step 1 — Log the prompt

Append the Phase 06 initiating prompt to `log.md`, verbatim.

### Step 2 — Mark all completed checklist items

Verify every checklist item in the Implementation Plan is correctly ticked.
Any item that was not executed must be explicitly noted as `[SKIPPED — reason]`.

*Steps 3–5 may run as three subagent units in parallel (governing skill → Subagent Execution).
Step 6 onwards stays in the main session.*

### Step 3 — Generate the technical document (`doc-technical.md`)

Write `{{PHASE_DIR}}/doc-technical.md` — the development team's record. Frontmatter:

```yaml
---
type: Doc
audience: technical
expandable: true
status: COMPLETE
description: <one-line technical summary>
---
```

Body — terse bullets under these fixed headings (this is seed material, not prose):

```markdown
# [FEATURE_NAME] — Technical Documentation (seed)

## Files Created
| File | Description |
|---|---|
| `path/to/file` | What it contains and why it was created |

## Files Modified
| File | Changes |
|---|---|
| `path/to/file` | What was changed and why |

## Files Deleted
| File | Reason |
|---|---|
| `path/to/file` | Why it was removed |

## Deviations from Plan
- Link to each deviation in `../phase-05-implementation/phase.md#deviations` with its
  approved rationale. If none: "None."

## Learnings & Pitfalls (this feature)
- [Insight relevant to future work — patterns, constraints, pitfalls]

## Results (if measured)
| Metric | Before | After | Improvement |
|---|---|---|---|
| [Metric] | [value] | [value] | [delta] |

- Leave as `[pending developer measurement]` if not yet available; must be filled before
  the bundle is closed.
```

### Step 4 — Generate the business document (`doc-business.md`)

Write `{{PHASE_DIR}}/doc-business.md` — for non-technical business stakeholders. The
substance is the **business process** the feature enables; UI/UX appears only as *where
that process surfaces*. Draw the process from Phase 01 (Feature Analysis), not from
the code. Frontmatter:

```yaml
---
type: Doc
audience: business
expandable: true
status: COMPLETE
description: <one-line capability summary>
---
```

Body — terse bullets under these fixed headings:

```markdown
# [FEATURE_NAME] — Business Process & Experience (seed)

## Capability
- What can now be done that could not before — in business terms.

## Business Process (end to end)
- Step-by-step flow of the process this feature enables, from trigger to outcome.
- Include human-in-the-loop or out-of-band steps where they exist.

## Where It Surfaces (UI/UX)
- The surface(s) through which the process is exercised (screen, page action, email,
  API, etc.). Populate only what exists — if there is no user-facing surface, say so and
  describe the non-UI touchpoints (e.g. notification emails).

## What Changes for the User
- Concrete before/after from the user's or stakeholder's point of view.

## Not Included
- Explicit boundaries — what this feature deliberately does not do (e.g. deferred to a
  later feature).
```

### Step 5 — Generate the executive document (`doc-executive.md`)

Write `{{PHASE_DIR}}/doc-executive.md` — for executive sponsors. A short, honest
**business-outcome brief** at feature grain. **TCO is explicitly out of scope** — do not
produce cost, licensing, or budget figures; the bundle does not contain those inputs and
fabricating them is a defect. Frontmatter:

```yaml
---
type: Doc
audience: executive
expandable: true
status: COMPLETE
description: <one-line outcome>
---
```

Body — terse bullets under these fixed headings:

```markdown
# [FEATURE_NAME] — Business Outcome Brief (seed)

## Capability Delivered
- The business capability that now exists, in one or two bullets.

## Risk Delta
- Risk closed by this feature: [...]
- Risk opened or accepted (e.g. acknowledged-imperfect model, deferred hardening): [...]

## What It Unblocks
- What this enables next — dependent features, go-live, revenue path, etc.

## Scope Note
- TCO / cost / budget: out of scope for this feature-level brief.
```

Keep every audience document as brief as the feature warrants. Terse is correct — these
are seeds for later expansion, not the documents an external reader will see.

**Reference the Phase 04 intent seeds.** Where `phase-04-specification/` contains
forward-looking `doc-executive.md` / `doc-business.md`, the Phase 06 (outcome) versions
should reconcile intent against outcome — e.g. "intended X → deviation Z → delivered Y" —
linking back to the Phase 04 seed rather than overwriting it. This makes the
promise-vs-outcome comparison explicit for each audience.

### Step 6 — Graduate durable knowledge to the project store

Close-out is the single point where bundle knowledge graduates to the durable store
`.speccen/` (see the Durable Store section of the governing skill). By now
every relevant fact is final — decisions, ADRs, what was implemented, deviations, lessons —
so this is the honest moment to decide what persists beyond the bundle.

**Gather candidates:** the bundle's ADRs (`{{FOLDER_NAME}}/adrs/`), the deviations and
learnings tagged as graduation candidates in Phase 05, any scoped overrides recorded in
Phase 03, and the lessons of rejected Phase 03 prototypes.

**Resolve graduation by two paths:**

1. **Automatic — overrides.** Any bundle ADR that overrides a Tier-2 (project-level) ADR
   graduates **by necessity**, no discretion: a standing rule with an undocumented exception
   is a broken rule. Write it to `.speccen/decisions/` as a `decision` entry,
   and write **both** cross-links — `Overrides: <tier-2 id> — scope: <feature> — parent still
   valid elsewhere` on the new entry, and `Has exception: <new id> — <feature>` on the
   existing Tier-2 entry. Neither is deleted.

2. **Discretionary — promotions.** For the remaining candidates, **propose** to the Architect
   which look genuinely project-wide (with the evidence for each), and let the Architect
   decide. Put the choice to the Architect as an interview round (**`speccen-interview`**) —
   one numbered question per candidate, `promote` or `keep local`, each with your proposed answer
   and its evidence. Promote only those
   the Architect approves. Default is *stays local* — a feature-specific ADR, deviation, or
   lesson that isn't project-wide is **not** graduated; it remains in the bundle and dies with
   it. This keeps the store clean and intentional.

*In `autonomous` mode path 2 is not put to anyone — see **Mode behaviour**.*

**Write graduated entries** following the Durable Store entry format exactly: correct `kind`
(decision / lesson / note), `evidence`, `weight`, UTC `created`/`updated`, `origin:
<this-bundle-ref>, graduated <UTC>`, the `[Index]` nav bar, and relationship links. Preserve
the originating id where one exists. Obey all six entry rules — above all, never invent a
rationale, and never file anything the Architect has not approved (overrides excepted, which
are necessity not discretion, but still Architect-sanctioned in Phase 03).

**Write the feature node** for this work as `features/<id>/node.md` (a feature is a *folder* —
see the Graph model in the governing skill). Fill the **lightweight spine** — `work-kind`
(feature | refactoring), `date` (close-out UTC), `touches` (component tags; reuse the exact
spelling of tags already in the store), and `produced` (the ids of the entries just graduated —
this list is already in hand from the step above, so the edge is free). **Omit** the
`reference` edge: Phase 08 is optional and most bundles never run it, so a node normally has no
reference. If Phase 08 is run later, that pass adds the edge. Add the optional edges
(`builds-on`, `see`) only where obvious and cheap. On each graduated decision/lesson/note,
write the inverse `produced-by` edge back to this feature. A node with only the spine is
complete — optional edges never gate close-out. If the bundle was rejected and reverted (no
shipped work), write **no** feature node.

**Graduate the specification.** Phase 04 assembled a `specification.html` (+ reduced
`specification.md`) — the feature's durable **intent**, and unlike the reference it already
exists at close-out. Copy both into the feature folder
(`features/<id>/specification.html` + `.md`) and set the node's `specification` edge to
`./specification.html`. (The `reference` edge stays omitted — Phase 08 adds it later if that
optional pass runs. When both exist, they cross-link: intent ↔ outcome.) If no specification
was produced, omit the edge.

**Regenerate** the store's `index.md` (grouped by kind: Decisions / Lessons / Notes /
Features) **and** `timeline.md` (the feature nodes, oldest-first) from node frontmatter.

If `.speccen/` does not yet exist and anything graduates, create it (the
`decisions/` `lessons/` `notes/` `features/` folders + `index.md` + `timeline.md`) first.

### Step 7 — Write the closing summary file

Write `{{SUMMARY_FILE}}` (`type: Summary`, `phase: "06 — Close-out"`,
`status: COMPLETE`, `phase_file: ./phase.md`, one-line `description`). This is the
bundle's internal front-door — it points to the three audience documents:

```markdown
---
type: Summary
phase: "06 — Close-out"
status: COMPLETE
phase_file: ./phase.md
description: <one-line feature outcome>
---

# Phase 06 Summary — Close-out

[Index](../index.md) · [State](../STATE.md) · [Log](../log.md)

---

**Work completed:** [what was implemented, in one or two lines]

**Outcome:** [result, incl. before/after if measured]

**Audience documents (expansion seeds — not for verbatim external sharing):**
- Executive → [doc-executive.md](./doc-executive.md)
- Business → [doc-business.md](./doc-business.md)
- Technical → [doc-technical.md](./doc-technical.md)

**Deviations:** [count / "none"] → [detail](../phase-05-implementation/phase.md#deviations)

**Graduated to durable store:** [N decisions, N lessons, N notes / "none"] → [.speccen/](../../.speccen/index.md)
```

### Step 8 — Final workflow state

Update `{{FOLDER_NAME}}/STATE.md`:
- `Current Phase` → `CLOSE-OUT PHASE`
- `Phase Status` → `COMPLETE`
- `Pending Architect Action` → `none`

### Step 9 — Close the core workflow

*In `autonomous` mode replace the closing message with the end-of-run report — see **Mode behaviour**.*

The core speccen workflow is complete at this phase. Inform the Architect, and name
the two optional passes available — the Architect may invoke either, both (in either order),
or neither:

> "Close-out phase is complete. The analysis bundle is fully updated, and the
> core speccen workflow for [FEATURE_NAME] is complete.
>
> Two optional passes are available if you want them:
> - **Retrospective** — a review of the implemented code against conventions,
>   security, and the plan, producing a Retrospective Report (and optional GitHub Issues).
> - **Reference** — a comprehensive technical reference document.
>
> [If any remain:] Prototype branches kept as evidence: `speccen/proto/<bundle-id>/…` — delete them
> when you no longer need them.
>
> Say the word to run either, or we can stop here."

### Step 10 — Final commit (if in git repository)

**Auto-commit the implementation** (if in git repository) — only what this run produced, never
"all changes":
- Check if the working directory is inside a git repository
- Check if git is properly configured (user.name and user.email set)
- If both checks pass, stage **only these paths**:
  - the files listed in `doc-technical.md` under Files Created, Files Modified and Files Deleted
    (a rename is its old path deleted and its new path created);
  - the bundle folder `{{FOLDER_NAME}}`;
  - the durable-store entries this close-out wrote under `.speccen/` (Step 6).

  Stage them by path — `git add -A -- <paths>` stages creations, changes and deletions alike —
  and never force-add an ignored file, so the local config file stays out.
- Before committing, compare with `git status`:
  - a changed file the implementation touched (it appears in the Phase 05 record) but
    `doc-technical.md` does not list means the record is incomplete: correct `doc-technical.md`
    first, then stage the file — never guess;
  - a listed file with no change: note it in `doc-technical.md`;
  - **any other uncommitted change** — including one you cannot attribute to this implementation —
    stays out of the commit. Tell the Architect which files were left out.
- Commit with message:
  ```
  feat: [FEATURE_NAME] - complete implementation [skip ci]

  Completed via speccen
  See the analysis bundle for full analysis and implementation details
  ```
- **No AI attribution in the commit — ever.** The message is only the lines above: no
  `Co-Authored-By:` trailer, no "Generated with"/"Co-authored" line, no mention of the AI, the
  model, or Anthropic. The Architect is the sole author (governing skill, Invariant 6). Commit
  with the message passed literally and add no trailer. If anything would append an AI
  co-author or attribution line, **remove it before the commit is finalised**. A commit with AI
  attribution must never be created.
- If any check fails, notify the Architect:
  > "Implementation complete but not committed. Please commit manually or enable git."
- This creates the final snapshot of this implementation's changes, ready for review, PR, or
  handoff to another agent/skill for validation.

---

## Mode behaviour

**`gated`** — Steps 1–10 apply as written.

**`autonomous`** (Close-out runs under the bundle `mode`) — Steps 1–5 and 10 apply as written.
Steps 6 to 9 change:

- **Step 6.** Path 1 (overrides), the feature node, the graduated specification and the
  regenerated store index and timeline are mechanical and run as written. Path 2 (discretionary
  promotions) is the Architect's, so do not ask. Write the candidates with their evidence under
  `<a id="graduation-proposal"></a> ## Graduation Proposal` in `{{PHASE_FILE}}` and leave it
  pending. Write nothing discretionary to `.speccen/`.
- **Step 7.** Add to the summary: `**Delegated decisions:** [count] →
  [delegated-decisions.md](../delegated-decisions.md)` and `**Graduation proposal:** pending →
  [detail](./phase.md#graduation-proposal)`.
- **Step 8.** `Pending Architect Action` → `Review the delegated decisions and decide the
  graduation proposal`.
- **Step 9.** Replace the closing message with the end-of-run report described in
  **Autonomous Execution** (Transitions and the end of a run). Name the optional passes; do not
  start them. If any delegated decision or gate check exists, recommend the Retrospective.
- **Deciding the proposal later.** When the Architect resumes and decides, put the proposal
  through the review round (`speccen-interview` → The review round), write the approved entries in the Durable
  Store entry format, update the feature node's `produced` list and the inverse `produced-by`
  edges, regenerate the store index and timeline, and set `Pending Architect Action` to `none`
  once no review is outstanding.

---

## What This Skill Does NOT Do

- Does not write or modify any code
- Does not commit unrelated uncommitted changes — the final commit holds only this run's files
- Does not run without the implementation having been accepted (by the Architect, or by its delegate in `autonomous` mode)
- Does not close the workflow before all three audience documents are written
- Does not fabricate TCO, cost, or budget figures in the executive document
- Does not write the audience documents as polished prose — they are terse expansion seeds
- Does not treat any audience document as shareable verbatim outside the development team
- Does not self-initiate — it is entered only on the acceptance (the Architect's signal in `gated` mode, the delegate's passed gate test in `autonomous` mode)

---

## Quality Criteria

Close-out Phase is complete when:

- [ ] Extensions: every file in `.speccen/extensions/close-out/` is in the Extensions table; every active one was applied, each applicable requirement met or marked "not applicable, because…"
- [ ] All checklist items are ticked or marked skipped with reason
- [ ] `doc-technical.md` is written (files created/modified/deleted, deviations, learnings, results)
- [ ] `doc-business.md` is written (business process end-to-end; UI/UX as where it surfaces)
- [ ] `doc-executive.md` is written (outcome brief; TCO explicitly out of scope, no fabricated figures)
- [ ] All three carry `type: Doc`, the correct `audience`, and `expandable: true`
- [ ] All three are written in STE-style (short active sentences, plain consistent vocabulary)
- [ ] Each document is terse, bulleted seed material under its fixed headings (stubbed if little to say)
- [ ] All deviations are linked with their rationale
- [ ] Before/after results are present in `doc-technical.md` (or explicitly pending measurement)
- [ ] `{{SUMMARY_FILE}}` is written and links to all three audience documents
- [ ] Durable-store graduation resolved: overrides graduated automatically (both cross-links written); project-wide candidates proposed to and decided by the Architect — or explicitly nothing to graduate (in `autonomous` mode: written as a pending Graduation Proposal)
- [ ] Feature node written (lightweight spine: `work-kind`, `date`, `touches`, `produced`; `produced-by` inverse on each graduated entry), unless the bundle was reverted; store `index.md` and `timeline.md` regenerated
- [ ] The Phase 04 specification is graduated into the feature folder and the node's `specification` edge is set
- [ ] The final commit holds only this run's files (the `doc-technical.md` lists, the bundle, the graduated store entries), and any files left out are reported
- [ ] `STATE.md` is `COMPLETE`
