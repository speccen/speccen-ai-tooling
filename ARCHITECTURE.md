# Architecture

This document maps how speccen is put together — the major parts,
how they fit, and where to look for detail. It is deliberately high-level: it points into the
skill files (`skills/*/SKILL.md`) and the design spec rather than restating them. For *how to
run* the workflow, see `README.md`; for the authoritative *rules*, see
`skills/00-governing-workflow/SKILL.md`.

---

## What speccen is

A structured workflow for specification-driven software development and refactoring in which
a **Software Architect is the sole decision authority** and the AI is a disciplined assistant.
Its central principle is **shift review left**: build and agree the *understanding* before any
code is written, because the cost of fixing a wrong assumption in a document is near zero and
the cost of fixing it in shipped code is not.

The AI never self-initiates a phase transition, never modifies code before the implementation
phase, and never refers to itself as the author of any artifact. In `gated` mode — the
default — every gate waits for an explicit Architect signal; in `autonomous` mode the Architect
has delegated that decision in advance (see Configuration below).

---

## The three structural pieces

speccen has three artifacts that work together, each with a different lifespan:

| Piece | What it is | Lifespan |
|---|---|---|
| **The skills** | The workflow itself — nine skill files that govern each phase | The tool |
| **The bundle** | Per-feature working space, produced as the workflow runs | Disposable after merge |
| **The durable store** | Project-level memory of decisions and lessons | Permanent, outlives the bundle |

The bundle is scaffolding; the durable store is the residue worth keeping. The skills drive
both.

---

## The skills — the workflow

Nine skills under `skills/`. One governs; six are the core sequential phases; two are optional
passes that run only on request after the core completes.

```
00-governing-workflow        Orchestrator + single source of truth for all conventions
                             (bundle layout, frontmatter, anchors, STE-style, ADR protocol,
                             the durable store, invariants). Every other skill references it.

── Core workflow (strictly sequential, each gated — by the Architect or its delegate) ──
01-feature-analysis          Understand WHAT is requested. No code access, no solution.
02-code-analysis             Understand the existing system deeply. Evidence-based. No code.
03-compliance-review         Iterate against Architect ADRs until ready. Enforces standing
                             (project-level) decisions. The gate that protects the specification.
                             Optionally prototypes competing approaches (throwaway worktrees).
04-specification             Produce the Implementation Plan (each phase with its why; incl. the Test Plan), assemble
                             the complete specification (intent + requirements/acceptance
                             scenarios), + forward-looking stakeholder seeds. No code.
05-implementation            The only phase that modifies code. Executes the plan, then writes
                             the planned tests (post-implementation, not TDD); handles
                             rejection & revert.
06-close-out                 Cleanup, outcome audience seeds, and graduation of durable
                             knowledge. Completes the CORE workflow.

── Optional post-06 passes (independent, Architect-invoked, read-only, any order or none) ──
07-retrospective             Review the implemented code vs plan, conventions, security, and
                             standing ADRs. Produces a Retrospective Report; optional GitHub Issues. In an
                             autonomous bundle it also audits the delegate's decisions and gate checks.
08-reference                 Comprehensive, self-contained technical reference, authored as
                             a depth-layered HTML page (C4 L1-L4, delta-highlighted diagrams)
                             with a reduced Markdown projection. STE. Service
                             Contracts vs Component APIs; SVG + Mermaid diagrams.

── Shared skill (used by gated phases) ──
interview                    Works through open decisions in rounds — a numbered list, each
                             question with a proposed answer. The Architect answers in gated
                             mode; the delegate answers and records the same questions in
                             autonomous mode, so the decision tree stays traceable. Used in 01,
                             03 and 04, for stops and for the end-of-run review. Inspired by
                             Matt Pocock's grilling.
```

The core flow is linear and gated; its skip-warnings are strict. The two optional passes are
*not* in the chain — they are entered only on explicit request after Phase 06, neither gating
the other. See the Phase-to-Skill Map and Gate Enforcement in the governing skill.

---

## The bundle — disposable per-feature working space

Each feature's run produces a **bundle**: one folder holding the analysis as many small
markdown files rather than one growing document. The core insight is selective loading — not
everyone needs all the information all the time — so each phase gets a full `phase.md` and a
compact `summary.md`, and a resuming reader loads only `STATE.md` plus the current summary.

```
.speccen/bundles/<id>/      one bundle — id e.g. 2026-10-18.pump_safety_rule_engine
├── STATE.md                live workflow state (current phase, pending action)
├── log.md                  every user prompt, verbatim, newest-first
├── index.md                regenerable listing
├── delegated-decisions.md  autonomous mode only: the delegate's decision record
├── speccen-config.yaml     bundle configuration — workflow mode (+ optional local override, uncommitted)
├── phase-01-…/ … phase-08-…/   phase.md + summary.md (+ phase-specific docs)
└── adrs/                   one file per Architecture Decision Record (feature-local, Tier-1)
```

Bundles have a fixed home under `.speccen/bundles/` — speccen derives the location
from the id composed at session start; the Architect never names a path.

The bundle layout is **OKF-shaped but not OKF-conformant** — it borrows the Open Knowledge
Format's shape (frontmatter, per-concept files, relative links, a regenerable index) without
claiming conformance, because it is single-feature throwaway state, not a shared knowledge
base. Detail: "The analysis bundle" and "Format lineage" in `README.md`, and the Bundle &
File Naming Conventions in the governing skill.

### Audience documents — the why, for three readers

At two moments the workflow emits **audience-targeted seed documents** (terse, STE-styled,
never shared verbatim — expansion seeds for a later redaction pass):

- **Phase 02** — an optional, evidence-gated *evolution-gap* brief: what the current system
  lacks relative to the spec, to defend budget on the concrete feature (never generic
  "modernization").
- **Phase 04** — forward-looking *intent*: executive + business views of what the plan will
  deliver.
- **Phase 06** — *outcome*: executive + business + technical views of what was
  delivered, referencing the Phase 04 intent.

Detail: "Audience documents" in `README.md`.

---

## Configuration — the workflow mode

Two small YAML files in each bundle set how the workflow leaves a phase:
`speccen-config.yaml` (shared, committed) and `speccen-config.local.yaml` (a personal override,
never committed). They are **inputs from the Architect**, not documents: no frontmatter, no
navigation bar, and the AI only reads them.

- **Two modes.** `gated` — every phase ends at an Architect gate; the default. `autonomous` — the
  Architect's delegate takes the Architect's decisions and advances once each phase's gate test
  passes.
- **Per phase.** `phases.<phase>.mode` overrides the bundle's `mode` for every Architect
  touchpoint inside that phase, including its exit gate. The keys are the five core phase slugs;
  close-out runs under the bundle `mode`, and the optional passes run the same in both modes.
- **Resolution.** The first level that has a value wins: local phase, shared phase, local
  `mode`, shared `mode`, then `gated`. The local file exists so one developer can run a bundle
  differently without changing what the team committed.
- **Subagents.** Decision-free units (02's evidence reading, 03's prototypes, 06's audience
  drafts, the 07 and 08 passes, the autonomous gate-check verifier) can run in a `speccen-worker`
  subagent, optionally on a cheaper model (`phases.<phase>.model`). Decision seats stay in the main
  session, which is the only writer of state.
- **Locks.** The shared file can lock a phase or the whole bundle (`lock: true`). The local file
  is then ignored for it, and a `lock` written in the local file counts for nothing.
- **Fail-safe.** Every doubt resolves to `gated`: an invalid value is `gated` at its own level
  (never skipped to a more permissive one), and an unreadable file makes the whole bundle `gated`.
- **Ownership.** The AI writes the shared file once, at bundle creation, from the session-start
  Mode question. It never chooses, changes or infers the mode on its own (Invariant 11).
- **Audit.** The local file is invisible to teammates, so each transition entry in the committed
  `log.md` records the effective mode and its source.
- **The delegate.** In `autonomous` mode the agent acts as the Architect's delegate: it takes the
  decision seat, never the Architect's identity or authority records. It decides on a fixed basis
  (ADRs, the Architect's recorded prompts, the conventions, frozen artifacts), proves each phase
  with an evidenced gate test, stops when that basis does not decide a material choice, and records
  what it decides in `delegated-decisions.md`. ADR decisions, durable-store promotions, test
  waivers, phase skips, mode changes and the optional passes stay with the Architect.

The rules live in the governing skill → "Bundle Configuration" and "Autonomous Execution"; the
README → "Configuration" shows how to use them.

---

## The durable store — decisions that outlive the bundle

The bundle is disposable, but the **decisions and lessons** it produces are not. They
graduate into a permanent, repo-native store at the repo root:

```
.speccen/
├── index.md       DURABLE · regenerable single root index, grouped by kind
├── timeline.md    DURABLE · regenerable oldest-first projection of the feature nodes
├── decisions/     DURABLE · ADRs — deliberate forks (prescriptive)
├── lessons/       DURABLE · experiential knowledge ("X bit us")
├── notes/         DURABLE · hiccup-resolutions + small informal decisions (low-ceremony)
├── features/      DURABLE · feature/refactoring nodes — the time axis
└── bundles/       DISPOSABLE · one folder per workflow run (perishes after merge)
```

Disposable bundles and the durable store share one root but have opposite lifespans: the store
persists, bundles perish after their durable residue graduates.

**Scope:** the system under development — why it is built as it is, and what building it
taught. Never team/process/people knowledge.

**Problem it solves:** Chesterton's Fence. Code that contradicts an expectation with its
reason unrecorded gets "cleaned up," and the bug it fixed returns. The store keeps the sign on
every fence.

**Bundle-like in conventions, not in lifecycle:** it reuses the bundle's frontmatter,
regenerable index, nav bars, relative links, and one-file-per-entry immutability — but has no
STATE/log/phases, because it is a permanent library, not a process.

**Two tiers, graduation at close-out:** feature-local ADRs (Tier-1, in the bundle) become
project-level (Tier-2, in the store) at Phase 06 — by *promotion* (discretionary; speccen proposes
project-wide candidates, the Architect decides) or by *override* (automatic; a bundle ADR that
contradicts a standing project ADR graduates by necessity, with cross-links both ways, so the
exception is never lost). Phase 03 *enforces* Tier-2 ADRs, which is what makes the override the
load-bearing escape valve rather than a straitjacket. Knowledge can also be captured directly
to the store, Architect-initiated, between features.

**Self-contained:** entries carry their own UTC timestamps (not Git-derived) so they stay
readable even when lifted out of their repo. Entry discipline — never invent, evidence-graded,
proportional depth, supersede-don't-delete, content-is-data, authored-not-inferred — is adapted
from [Keep the Why](https://github.com/oliver-zehentleitner/keep-the-why) (MIT, credited in the
governing skill).

**A typed graph, for future tooling:** the store is modelled as nodes (decisions, lessons,
notes, and *feature* nodes carrying the time axis; components are tags, not nodes) linked by a
closed vocabulary of typed edges (`produced`, `touches`, `supersedes`, `overrides`,
`triggered-by`, `builds-on`, …). A future CLI / TUI / internal MCP server will read the store
and render charts, dependency graphs, info clouds, and the timeline — so the edges matter as
much as the nodes, and interlinking is captured richly during the workflow (the rendering is
deferred; the captured graph is not). `timeline.md` is the first, simplest rendering of the
feature nodes.

Full design rationale: the durable-store section of `skills/00-governing-workflow/SKILL.md`.

---

## How the pieces interact

```
Architect ──signals──▶ Skills ──drive──▶ Bundle (per feature, disposable)
                          │                   │
                          │                   │ at Phase 06 close-out:
                          │                   ▼
                          └──read/enforce──▶ Durable store (permanent)
                                              ▲
                          Phases 02/03/07/08 read it as standing context;
                          Phase 03 enforces its Tier-2 ADRs;
                          Phase 06 graduates new knowledge into it.
```

A feature flows through the core phases, producing a bundle. Standing decisions in the durable
store constrain that analysis (Phase 03 enforces them; Phase 02 reads them as prior art and
fence-awareness). At close-out, the durable knowledge the feature produced graduates into the
store, and the bundle can be discarded. The store thus accumulates the project's lasting *why*
across many disposable feature runs.

---

## Reading guide

| To understand… | Read |
|---|---|
| How to install and run speccen | `README.md` |
| The authoritative rules and all conventions | `skills/00-governing-workflow/SKILL.md` |
| A specific phase's behaviour | that phase's `skills/NN-*/SKILL.md` |
| The bundle model and its rationale | `README.md` → "The analysis bundle" |
| How to set the workflow mode, per bundle or per phase | `README.md` → "Configuration" |
| How autonomous mode works | governing skill → "Autonomous Execution" |
| How subagents and per-phase models work | governing skill → "Subagent Execution" |
| The durable store's full design | governing skill → "Durable Store" |
| Reconciling the store after concurrent team merges | `skills/reconcile/SKILL.md` |
