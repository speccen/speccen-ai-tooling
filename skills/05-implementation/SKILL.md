---
name: speccen-implementation
description: the Implementation Phase of speccen. Use when executing an approved Implementation Plan, and handling rejection and revert cycles. Close-out of the completed work is handled by the separate Close-out skill (Phase 06).
---

# speccen — Implementation Skill
## IMPLEMENTATION PHASE

> File paths, frontmatter, anchors, and the navigation bar in this skill follow the
> **Bundle & File Naming Conventions** defined in `skills/00-governing-workflow/SKILL.md`.
> Every `phase.md` and `summary.md` carries the navigation bar directly under its H1 title;
> `doc-*.md` and ADR files do not.
> Phase 05 writes to `{{FOLDER_NAME}}/phase-05-implementation/` (`phase.md`, `summary.md`).
> When the Architect or its delegate accepts the implementation, the workflow advances to the separate
> **Close-out skill** (`skills/06-close-out/SKILL.md`).

---

## Purpose

This skill governs the Implementation Phase of speccen.
Implementation Phase is the first phase in which the codebase is touched. The Implementation
Plan produced in Phase 04 is the sole guide for all changes made.

The Architect retains full authority to reject the implementation at any point; in `autonomous`
mode the delegate also rejects it when the gate test fails.
A rejection triggers Phase 05b — revert, learn, and re-enter. Once the Architect
**accepts** the implementation, the workflow advances to Phase 06 (Documentation &
Cleanup), governed by its own skill.

---

## Inputs

- The completed analysis bundle, including the Implementation Plan in `phase-04-specification/phase.md`
- Access to the codebase via Claude Code CLI or Copilot CLI
- The source code repository (for revert capability in Phase 05b)

---

## Implementation Constraints

These constraints apply throughout Phase 05 and cannot be overridden by prompt wording:

- **Simplicity First** — make every change as simple as possible. Minimize code impact.
  Choose the simplest solution that satisfies the requirements. Avoid over-engineering.
  Prefer small, focused changes over large refactorings unless explicitly required by the
  Implementation Plan.
- **No Laziness** — identify root causes for the request.
  Do not apply superficial fixes or workarounds. Understand why the change is needed and
  address the underlying problem. If the Implementation Plan is based on symptoms rather
  than root cause, flag it as a deviation and return to analysis.
- **Avoid Temporary Fixes** — do not introduce TODO comments, placeholder implementations,
  or "we'll fix this later" code. Every change must be production-ready. If a proper
  solution cannot be implemented now, flag it as a deviation and discuss with the Architect.
- **Senior Developer Standards** — apply the code quality, testing rigor, and engineering
  discipline expected of a senior developer. Write code you would be proud to have reviewed
  by your peers. Consider maintainability, readability, and long-term consequences.
- **Minimal Impact** — touch only what is necessary to satisfy the Implementation Plan.
  Do not refactor unrelated code, rename variables "for consistency", or apply coding
  standards to files you're not already modifying. Every line changed increases the risk
  of introducing new bugs. Avoid scope creep.
- **No new dependencies** — do not introduce new NuGet packages or external libraries
  without explicit Architect approval. If a dependency appears necessary, stop and raise
  it as a deviation before proceeding.
- **No unnecessary comments** — do not add explanatory comments to code unless they
  were present in the original or are explicitly required by the Implementation Plan.
- **Null safety is critical** — every new or modified code path must be reviewed for
  null dereference risk before moving on. Null-related bugs are the most common source
  of regression. When in doubt, add a null check.
- **Continuous validation** — do not wait until the end of a phase to build and check.
  After every meaningful code change, verify the build is clean and no new warnings
  have been introduced.

---

## Phase 05a — Implementation Execution

For this phase, `{{PHASE_FILE}}` = `{{FOLDER_NAME}}/phase-05-implementation/phase.md`
and `{{SUMMARY_FILE}}` = `{{FOLDER_NAME}}/phase-05-implementation/summary.md`.
Initialise `{{PHASE_FILE}}` with frontmatter (`type: Phase`, `phase: "05 — Implementation"`,
`status: IN PROGRESS`, one-line `description`) and emit the common anchors plus the
phase-specific anchors `deviations` and `implementation-summary`.

**Extensions.** Before Step 1, apply the active extensions in `.speccen/extensions/implementation/`
(governing skill → Extensions) and record them in the Extensions table of `{{PHASE_FILE}}`. They
add to or tighten this phase; they never relax it.

### Step 1 — Log the prompt

Append the Phase 05 initiating prompt to `log.md`, verbatim.

### Step 2 — Confirm the Implementation Plan

Before writing a single line of code, complete the following confirmations:

#### 2.1 — Re-read project conventions in great detail

The `.claude/` and `.github/` folders contain the authoritative rules for this codebase.
**Re-read every .md file thoroughly** to refresh understanding before touching any code:

- `.claude/CLAUDE.md` and all other `.md` files — read everything deeply
- `.github/` folder — read all `.md` files, understand all coding standards and workflows

Create a **Project Conventions Re-Confirmation** section in `{{PHASE_FILE}}`:

```markdown
### Project Conventions Re-Confirmation (Phase 05)

#### `.claude/CLAUDE.md`
**Re-Read:** ✓  
**CRITICAL/MANDATORY Rules Impacting This Implementation:**
- [Any CRITICAL/MANDATORY rules that apply to files being modified — extract verbatim]

**Other Rules Impacting This Implementation:**
- [Architecture boundaries that constrain this change]
- [Technology constraints that apply to files being modified]
- [Naming conventions to follow]
- [Critical workflows this implementation must respect]
- [Hard constraints that cannot be violated]

#### `.github/github-instructions.md` (or similar)
**Re-Read:** ✓  
**CRITICAL/MANDATORY Rules Impacting This Implementation:**
- [extract if applicable]

**Other Rules Impacting This Implementation:**
- [List only rules directly relevant to the Implementation Plan]

#### `instructions/csharp-instructions.md` (or similar)
**Re-Read:** ✓  
**CRITICAL/MANDATORY Rules Impacting This Implementation:**
- [extract if applicable]

**Other Rules Impacting This Implementation:**
- [List only rules directly relevant to the Implementation Plan]

[Repeat for each .md file]
```

Focus on rules that directly affect the Implementation Plan being executed.
This is not a full re-extraction — it's a targeted confirmation of rules that
matter for the specific code being written.

**CRITICAL**: Any rule marked CRITICAL, MANDATORY, IMPORTANT, DO NOT, NEVER, ALWAYS
that affects the code being written MUST be listed verbatim. These are non-negotiable
constraints on the implementation.

#### 2.2 — Review the Implementation Plan

Re-read the Implementation Plan in `phase-04-specification/phase.md`.

Confirm with the Architect (in `autonomous` mode the delegate confirms the plan itself — see
**Mode behaviour**):

> "I am about to begin implementation following the approved plan. I will execute
> Phase 1 first: [brief description]. I will work through all phases and tasks without
> stopping, marking each item complete as I go. Please confirm I should proceed."

Wait for explicit confirmation.

### Step 3 — Execute to completion

Execute the entire Implementation Plan without stopping between phases, unless:
- A deviation from the plan is encountered (invoke Deviation Protocol)
- A build error cannot be resolved without Architect input
- A null safety risk is identified that the plan does not address

For each checklist item:
1. Implement it
2. Validate — build clean, no new warnings, null safety reviewed
3. Tick it in the Implementation Plan: `- [x]`
4. Proceed immediately to the next item

At the end of each phase, report progress to the Architect and continue to the
next phase without waiting for acknowledgement, unless a gate condition above is met.

**Do not skip checklist items.** If an item cannot be completed as written, stop
immediately and invoke the Deviation Protocol.

### Step 4 — Write and run the planned tests

After the implementation checklist is complete, execute the **Test Plan** approved in Phase 04
(`phase-04-specification/phase.md`). Tests are written **now — after the code**, not
before (speccen does not use TDD); but their assertions come from the plan and the contract, not
from the code just written (see the provenance rule below).

For each item in the Test Plan's scope:
1. Write the unit or integration test(s) as planned, following the project's test conventions
   (captured in Phase 02).
2. Each test must establish a **behaviour the plan called for** — especially the error/failure
   behaviour, side effects, boundary/edge cases, and ordering/idempotency the plan singled out.
3. Run the tests. They must pass. Fix the **code** when a test derived from the contract fails
   — a failing contract-derived test is a real defect, not a test to adjust.

**Assertion provenance (critical).** Derive every assertion from the Test Plan, the contract,
and the requirement — **never reverse-engineer expected values from the implementation that was
just written.** The code is the loudest signal in view and the most dangerous source: a test
that asserts "what the code does" certifies whatever the code guessed, including a subtly wrong
behaviour the AI filled in from training rather than from the Architect's intent. The test's
job is to catch that divergence, which it can only do if its assertions trace to intent, not to
code. If the Test Plan is ambiguous about an expected behaviour, ask the Architect — do not
resolve it by reading the implementation.

**A "none, because …" entry** in the Test Plan is honoured — write no test there. If the
Architect waives a planned test during implementation, record it as a deviation (Step 5).

Green planned tests are part of what "implementation is complete" means: the implementation is
not ready for the accept gate until the Test Plan's tests are written and passing
(or explicitly waived).

### Step 5 — Deviation Protocol

If at any point the implementation requires deviating from the approved plan:

1. **Stop immediately** — do not continue implementing
2. Document the deviation in `{{PHASE_FILE}}` under `<a id="deviations"></a> ## Deviations`:

```markdown
### Deviation [N] — YYYY-MM-DD

|     | Info |
| --- | --- |
| **Planned** | [What the Implementation Plan specified] |
| **Actual** | [What was actually implemented or discovered] |
| **Reason** | [Root cause — why the deviation was necessary] |
| **Impact** | [What this changes about the remaining plan] |
| **Purpose preserved?** | [Yes / No — does the plan phase's **Why** still hold after the deviation?] |
| **Decision** | PENDING |
```

3. Notify the Architect:
   > "I have encountered a deviation from the Implementation Plan. I have stopped
   > implementation and documented it under the Deviations section of
   > `phase-05-implementation/phase.md`. Please review and signal how to proceed."

4. Wait for Architect decision before continuing.

5. When the Architect decides, update the deviation entry Decision field:
   - `APPROVED — [rationale]` → continue with the deviation
   - `REJECTED — [rationale]` → revert to the plan as written

*In `autonomous` mode steps 3–5 are replaced — see **Mode behaviour**.*

**Example deviation:**

```markdown
### Deviation 1 — 2026-03-10

|     | Info |
| --- | --- |
| **Planned** | Conditional thinking block rendered inside the bot message `<Padder>` using `@if (!string.IsNullOrEmpty(message.ThinkingContent))` as a child of `<Padder>`. |
| **Actual** | `<Padder>` in RazorConsole does not support dynamic child counts. Placing `@if` blocks inside `<Padder>` (including the `@if (message.IsUser)` branch) caused the component to fail to render any children — neither the thinking block nor the response block appeared. |
| **Reason** | The original codebase establishes a clear convention: `<Rows>` handles dynamic/conditional children; `<Padder>` always receives a fixed set of children. The Implementation Plan specified the template structure but did not account for this RazorConsole-specific constraint. |
| **Impact** | Phase 3.2 (rendering loop) must be restructured. Conditional content must live at the `<Rows>` level. Each `<Padder>` must have exactly 3 fixed children. Two separate `<Padder>` instances are used for bot messages: one conditional Padder for thinking content, one always-present Padder for the response. |
| **Purpose preserved?** | Yes — the phase still renders the conditional thinking block; only its placement changes. |
| **Decision** | APPROVED — self-evident fix from the existing codebase convention. |
```

**Durable-store graduation candidates.** A deviation often *is* a reusable hiccup-resolution
("the system resisted X; we did Y because Z") — exactly the kind of knowledge the durable
store keeps so the fence keeps its sign. When a deviation (or a learning, below) has
relevance beyond this one feature, mark it as a **graduation candidate** in `{{PHASE_FILE}}`
(a short `graduation-candidate: <why it may be project-wide>` note beside the deviation). This
does not graduate anything now — graduation is resolved only at Phase 06 close-out — it just
hands close-out a clean list to review. If a deviation implements a sanctioned divergence from
a Tier-2 ADR recorded in Phase 03 (a scoped override), note that too: it graduates by
necessity at close-out. Do not write to `.speccen/` during Phase 05.

### Step 6 — Update workflow state after each phase

After each implementation phase is complete, update `{{FOLDER_NAME}}/STATE.md`:
- `Phase Status` → `IN PROGRESS — Implementation Phase [N] complete`
- `Last Updated` → today's date
- `Pending Architect Action` → `Review Phase [N] output. Signal to proceed to Phase [N+1].`

### Step 7 — On acceptance, hand off to Phase 06

*In `autonomous` mode the notification and the wait are replaced by the gate test — see **Mode behaviour**.*

When all implementation phases are complete **and the Phase 04 Test Plan's tests are written
and passing** (or explicitly waived), write `{{SUMMARY_FILE}}`
(`{{FOLDER_NAME}}/phase-05-implementation/summary.md`) — work completed, outcome, the test
result (what was tested, all green / waived items), and any deviations, linking back into
`{{PHASE_FILE}}`'s anchors — then notify the Architect:

> "Implementation is complete and the planned tests pass [or: with the following waived: …].
> Please review and either accept, or reject (which triggers Rejection & Revert). On
> acceptance, the workflow advances to Close-out."

On the **acceptance** — the Architect's signal, or the delegate's passed gate test in `autonomous`
mode — the Governing Workflow advances to Phase 06,
governed by `skills/06-close-out/SKILL.md`. This skill does not perform documentation itself.

---

## Mode behaviour

**`gated`** — Phase 05a and Phase 05b apply as written.

**`autonomous`** — the delegate takes the Architect's decisions in this phase:

- **Step 2.** Write the Project Conventions Re-Confirmation as usual. Confirm the Implementation
  Plan yourself: record "Plan confirmed by the delegate" with the date in `{{PHASE_FILE}}`, and do
  not wait.
- **Step 3.** Execute as written. The report after each plan phase (Step 6) is a progress report
  and does not stop the run. A new dependency, a build error you cannot resolve and a
  null-safety risk the plan does not address are stops.
- **Step 5 (deviation).** Stop implementing and document the deviation (steps 1–2). Then, if it
  changes no acceptance scenario, no contract and no scope, and the phase's **Why** still holds,
  decide it: set `Decision` to
  `APPROVED (delegated) — [rationale]` or `REJECTED (delegated) — [rationale]`, record it in
  `delegated-decisions.md`, and continue. If it changes any of them, or defeats the phase's **Why**, stop. Never waive a planned
  test.
- **Step 7 (accept).** Instead of notifying and waiting, run the gate test: the Phase 05a
  criteria below; every acceptance scenario of the specification has a passing planned test
  (cite the test); no waiver; every deviation has a decision; the delivered behaviour matches the
  scenarios (cite files and tests). If it passes, the delegate accepts and the workflow advances
  to Phase 06. If it fails, fix once and test again. If it still fails, the delegate rejects:
  run Phase 05b.
- **Phase 05b.** The rejection reason is the failed check, recorded verbatim. Revert only the
  files this iteration changed; if that cannot be established — unrelated uncommitted changes in
  those files, or no repository — stop. Verify the revert instead of asking the Architect to
  confirm it. Record plan updates as delegated decisions, not ADRs, and re-enter Phase 05a.
  After two rejected iterations, stop.
- Write `## Delegate gate check` into `{{SUMMARY_FILE}}`. Decision basis, stop conditions and
  records: **Autonomous Execution** in the governing skill.

---

## Phase 05b — Rejection & Revert

If the Architect rejects the implementation (in `autonomous` mode the delegate rejects it — see
**Mode behaviour**):

### Step 1 — Stop all implementation activity immediately

Do not make further changes to the codebase.

### Step 2 — Document the rejection

Add to `{{PHASE_FILE}}` under `## Implementation Iterations`:

```markdown
### Implementation Iteration [N] — REJECTED [YYYY-MM-DD]
**Rejection reason:** [the stated reason, verbatim — the Architect's, or in `autonomous` mode the failed check]
**Reverted:** [YYYY-MM-DD]
```

### Step 3 — Revert the codebase

The changed files are reverted via the source code repository.
Confirm with the Architect that the revert is complete before proceeding (in `autonomous` mode
the delegate verifies it).

### Step 4 — Extract learnings

Analyse what went wrong and document it under the same section:

```markdown
### Learnings from Iteration [N]
- [Learning 1 — what assumption was wrong, what was missed, what should change]
- [Learning 2]
- ...
```

A learning with relevance beyond this feature is a durable-store **graduation candidate**
(a `lesson`) — mark it as such for Phase 06 to review, exactly as for deviations above. Note,
though, that a rejected-and-reverted iteration whose bundle never reaches close-out will not
graduate its candidates — which is correct, since the reverted code shipped nothing.

### Step 5 — Update the Implementation Plan

Based on the learnings, propose updates to the Implementation Plan that address
the rejection reason. These updates are treated as ADRs — the Architect must
review and approve the updated plan before Phase 05a re-enters.

### Step 6 — Update workflow state

Update `{{FOLDER_NAME}}/STATE.md`:
- `Phase Status` → `AWAITING ARCHITECT REVIEW`
- `Pending Architect Action` → `Review updated Implementation Plan. Signal go for next implementation iteration.`

---

## What This Skill Does NOT Do

- Does not begin implementation without the Architect's go signal (`gated`) or a passed delegate gate check at the Phase 04 exit (`autonomous`)
- Does not skip checklist items silently
- Does not continue past a deviation without a decision by the Architect or its delegate
- Does not self-accept an implementation — only the Architect accepts, or its delegate in `autonomous` mode after a passed gate test
- Does not perform documentation & cleanup — that is Phase 06's separate skill

---

## Quality Criteria

### Phase 05a complete when:
- [ ] Extensions: every file in `.speccen/extensions/implementation/` is in the Extensions table; every active one was applied, each applicable requirement met or marked "not applicable, because…"
- [ ] Project conventions re-read and re-confirmed before implementation started
- [ ] All Implementation Plan checklist items are ticked
- [ ] All deviations are documented with decisions by the Architect or its delegate
- [ ] Each deviation states whether its phase's **Why** still holds
- [ ] Each phase ended with a build-and-validate step
- [ ] The Phase 04 Test Plan's tests are written and passing (or explicitly waived, recorded as deviations)
- [ ] Test assertions derive from the plan/contract, not from the just-written code
- [ ] `STATE.md` is updated after each phase
- [ ] `autonomous`: `## Delegate gate check` is written in the summary, with evidence for every item

### Phase 05b complete when:
- [ ] Rejection is documented with the reason verbatim (the Architect's, or in `autonomous` mode the failed check)
- [ ] Revert is confirmed
- [ ] Learnings are documented
- [ ] Updated Implementation Plan is approved by the Architect or its delegate (in `autonomous` mode a delegated decision, not an ADR)
