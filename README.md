# speccen

**speccen** [ˈspɛkŋ] · West Flemish · noun
*A sweet spot for AI engineering.*

*Spekken* means candy in West Flemish. We make specification-driven software development a
little sweeter.

**Specification-driven. Lean by design. Autonomous by choice.**

speccen takes a request — a feature, refactoring, fix or idea — through six core phases — from deep analysis, to an
agreed specification, to implementation and close-out — with two optional post-completion
passes. A Software Architect decides; the AI assists (Claude Code CLI or Copilot CLI).

> For a high-level map of how the whole system fits together — the skills, the disposable
> per-feature bundle, and the durable project store — see [`ARCHITECTURE.md`](ARCHITECTURE.md).

---

## Skills

| Folder | Skill | Phase |
|---|---|---|
| `00-governing-workflow/` | Governing Workflow | All phases — orchestrator |
| `01-feature-analysis/` | Feature Analysis | FEATURE ANALYSIS PHASE |
| `02-code-analysis/` | Code Analysis | CODE ANALYSIS PHASE |
| `03-compliance-review/` | Compliance & Review | COMPLIANCE & REVIEW PHASE (iterative) |
| `04-specification/` | Specification | SPECIFICATION PHASE |
| `05-implementation/` | Implementation | IMPLEMENTATION PHASE (incl. rejection & revert) |
| `06-close-out/` | Close-out & Seeds | CLOSE-OUT PHASE |
| `07-retrospective/` | Retrospective | RETROSPECTIVE PHASE *(optional, post-06)* |
| `08-reference/` | Reference | REFERENCE PHASE *(optional, post-06)* |
| `reconcile/` | Store Reconciliation | *not a phase — repo-level store op, run on main after a merge* |
| `interview/` | Interview | *not a phase — works through open decisions in rounds of numbered questions, each with a proposed answer; you answer in `gated` mode, the delegate records its own answers in `autonomous` mode* |

The interview skill is inspired by [grilling](https://github.com/mattpocock/skills/tree/main/skills/productivity/grilling)
by Matt Pocock (MIT).

Each workflow run produces an **analysis bundle** — a folder of markdown files rather than
a single document. See [Core Principles](#core-principles) for the layout and the reasoning
behind it.

---

## Installation

### Via Claude Code CLI (recommended)

Add the marketplace, then install the plugin:

```bash
/plugin marketplace add speccen/speccen-ai-toolkit
/plugin install speccen@speccen-ai-toolkit
```

### Via GitHub Copilot CLI

```bash
# Add the marketplace
copilot plugin marketplace add speccen/speccen-ai-toolkit

# Install the plugin
copilot plugin install speccen@speccen-ai-toolkit
```

### Via VS Code Extension (easiest for VS Code users)

Install the [Agent Plugins extension](https://github.com/timheuer/vscode-agent-plugins) for VS Code, which provides a UI for managing marketplaces and skills:

1. Install the extension from the VS Code marketplace
2. Open the Command Palette (`Ctrl+Shift+P` / `Cmd+Shift+P`)
3. Run: `Agent Plugins: Add Marketplace`
4. Enter: `https://github.com/speccen/speccen-ai-toolkit.git`
5. Install the skill scoped to your workspace or user settings

The extension makes it easy to browse, install, and manage skills without command-line tools.

### Local installation

Copy the `skills/` folder to your project or user skills directory, and the `agents/` folder for
subagent support:

```bash
# Project-level (shared via version control)
cp -r skills/ .claude/skills/
cp -r agents/ .claude/agents/

# User-level (available in all your Claude Code sessions)
cp -r skills/ ~/.claude/skills/
cp -r agents/ ~/.claude/agents/
```

### Auto-install for your team

Add to `.claude/settings.json` in your project repository so team members who trust
the project folder are offered the marketplace and the plugin:

```json
{
  "extraKnownMarketplaces": {
    "speccen-ai-toolkit": {
      "source": {
        "source": "github",
        "repo": "speccen/speccen-ai-toolkit"
      }
    }
  },
  "enabledPlugins": {
    "speccen@speccen-ai-toolkit": true
  }
}
```

## How to Start

On Claude Code, use the shortcut command:

```
/speccen:speccen-start
```

(Type `speccen-start` in the command picker to filter to it. It takes an optional feature name;
the workflow asks for anything missing.) The command is a thin shortcut — it defers to the
Governing Workflow skill, which is the single source of truth.

On any host, or without the command, start by loading the skill directly:

```
Read skills/00-governing-workflow/SKILL.md and follow its Session Start Protocol.
```

Either way, the Governing Workflow skill handles everything from there — new session or
resume. A new session asks for an id prefix, the feature name, the work kind and the mode
(see [Configuration](#configuration)).

---

## Configuration

Each bundle carries two small YAML files that set the **workflow mode** — how the workflow
leaves a phase:

| File | Committed | Purpose |
|---|---|---|
| `speccen-config.yaml` | Yes — with the bundle | The bundle's shared configuration |
| `speccen-config.local.yaml` | No — the bundle's own `.gitignore` ignores it | Your personal override |

`speccen-config.yaml` is created when the bundle starts: the **Mode** question at session start
sets its `mode`. The local file does not exist by default — it appears only when you ask for it.
It lets one developer run a bundle differently from the team (the bundle is committed `gated`,
and a colleague chooses otherwise for their own sessions) without changing what the team
committed. Both files are plain YAML with no frontmatter, and you edit them by hand.

### Modes

- **`gated`** — the default. Every phase ends with a stop; nothing advances without your go.
- **`autonomous`** — the Architect's delegate decides and advances, stopping when your recorded
  intent does not decide.

### Autonomous mode

In `autonomous` mode an agent acts as **your delegate**. It takes your decisions in the phase —
signalling readiness, approving the plan, accepting the implementation — but not your identity
and not your authority records.

- **It decides only on what you recorded:** your ADRs and the project's standing decisions, your
  own prompts (kept verbatim in `log.md`), the project conventions, and earlier artifacts once
  frozen (the specification freezes when Phase 04 closes).
- **It tests itself at every phase end.** A gate test checks the phase's own criteria, item by
  item, with cited evidence, and writes the result into that phase's summary under
  `## Delegate gate check`. Phase 04 adds a traceability check: every requirement and ADR is
  covered by a scenario, every scenario by a test, and nothing is invented. Phase 05 accepts only
  if every scenario has a passing test and every deviation has a decision.
- **It stops when your recorded intent does not decide** a material choice — a contract, the data
  model, security, a new dependency — or when ADRs conflict, a gate test keeps failing, a rejected
  implementation has been retried twice, or an action cannot be undone. It collects those questions
  into one numbered round with its proposed answers; you answer "yes", "no, use X" or "later",
  and it carries on.
- **It records what it decides.** Non-trivial choices go into `delegated-decisions.md` as `DD-n`
  entries marked "decided under delegation", for you to adopt or override. Every interview
  question it answers is recorded with who answered it, so you can follow its decision tree. It never decides an ADR — it can only raise a pending
  placeholder question, as Phase 03 does today, and, only if you ask, record your stated decision
  into it verbatim.
- **Some things stay yours:** deciding ADRs, promoting knowledge to the durable store, waiving a
  test, skipping a phase, changing the mode, and starting the optional passes (retrospective and
  reference).
- **A run ends at close-out**, with one numbered review round: each delegated decision (adopt,
  override or later) and each graduation candidate (promote, keep local or later).

### Per-phase overrides

```yaml
# speccen-config.yaml
mode: autonomous
phases:
  specification:
    mode: gated        # stop here: you review the specification before implementation starts
    lock: true         # and no local file can remove this stop
  implementation:
    mode: gated        # stop here: you accept the implementation before close-out
```

This bundle runs autonomous, except that it stops at the specification and at the implementation
accept. The specification stop is locked, so a local file cannot remove it.

A phase's `mode` governs every point inside that phase where you would decide, including the gate
at its end. The phase keys are `feature-analysis`, `code-analysis`, `compliance-review`,
`specification` and `implementation`. Close-out has no exit gate and no key: it runs under the
bundle `mode`. The optional `retrospective` and `reference` passes are always started by you, so
they have no key either.

Phase 03 can also take `prototypes: <n>` (`phases.compliance-review.prototypes`, default `0`). In
`autonomous` mode it is how many prototypes the delegate may build for one material choice
before it stops with a comparison for you to decide. Prototypes are optional and live outside your
working tree; see Phase 03.

Phases can also set a `model` for their **subagent units** — decision-free work such as Phase 02's
evidence reading, Phase 03's prototypes, Phase 06's audience drafts, and the Retrospective and
Reference passes:

```yaml
phases:
  code-analysis:
    model: haiku      # its evidence reading runs on haiku; the phase's decisions stay on your session's model
```

Labels are `haiku`, `sonnet`, `opus`, a full model id, or `inherit` (the default). If your host
names models differently, map them in your local file under `models:`. Gates, ADRs and the
delegate's choices always run on your session's model. Subagents need host support: Claude Code
loads the plugin's `agents/speccen-worker.md`. Where that is not available, the work runs inline on
your session's model and the AI tells you.

### Which setting wins

For the phase being left, the first value found wins:

1. your local file → `phases.<phase>.mode`
2. the shared file → `phases.<phase>.mode`
3. your local file → `mode`
4. the shared file → `mode`
5. `gated`

A team can stop a local file from changing a phase by locking it in the **shared** file:
`phases.<phase>.lock: true` for one phase, or `lock: true` at the top level for the whole bundle.
A locked phase ignores your local file, and the AI tells you once. A `lock` written in the local
file is ignored.

### Safety

- Every doubt resolves to `gated`: an invalid value is `gated` at the level where it was
  written, and an unreadable file makes the whole bundle `gated` until it is fixed.
- The AI never chooses or changes the mode on its own. Saying "just run through it" in chat
  changes nothing; it offers to put the setting in your local file and writes it only after you
  confirm.
- Before it creates your local file, the AI checks that the bundle's `.gitignore` ignores it, adds
  the line if it is missing, and refuses to create the file if it still cannot make sure.
- Each phase transition in `log.md` records the mode that applied and where it came from
  (`default`, `shared config` or `local config`), so the committed history shows what ran even
  though the local file is not committed.
- Each phase boundary is committed as usual when the project is a git repository, so every
  autonomous phase has a revert point.

---

## Core Principles

### The analysis bundle

The **analysis bundle** is the persistent memory of a workflow run — a single folder,
one per request, holding the entire analysis as a set of small markdown
files rather than one growing document. Its shape:

```
.speccen/bundles/<id>/     # one bundle; id e.g. 2026-10-18.pump_safety_rule_engine
├── index.md                              # regenerable root listing — links + descriptions, no live state
├── STATE.md                              # live workflow state: current phase, status, pending action
├── log.md                                # every user prompt, verbatim, date-grouped newest-first
├── delegated-decisions.md                # autonomous mode only: decisions the delegate took, for you to adopt or override
├── speccen-config.yaml                   # bundle configuration: workflow mode, with optional per-phase overrides — committed
├── speccen-config.local.yaml             # optional personal override — never committed (the bundle's .gitignore ignores it)
├── .gitignore                            # one line: speccen-config.local.yaml
├── files/                                # supporting files for this request — always committed
│   └── .gitkeep                          # empty — keeps the folder in git
├── phase-01-feature-analysis/
│   ├── phase.md                          # full analysis content for the phase
│   └── summary.md                        # compact "what happened / outcome / open items", sorts last
├── phase-02-code-analysis/
│   ├── phase.md
│   ├── summary.md
│   └── doc-evolution-gap.md              # optional: current-state gap vs specs (budget case)
├── phase-03-compliance-review/
│   ├── phase.md
│   ├── summary.md
│   └── prototype-<N>.md                  # optional: one per prototype run, with its outcome
├── phase-04-specification/
│   ├── phase.md
│   ├── summary.md
│   ├── specification.html                # the complete assembled spec (intent + requirements/scenarios)
│   ├── specification.md                  # reduced projection of it
│   ├── doc-executive.md                  # intent seed: business-outcome brief (forward-looking)
│   └── doc-business.md                   # intent seed: business process & experience (forward-looking)
├── phase-05-implementation/
│   ├── phase.md
│   └── summary.md
├── phase-06-close-out/
│   ├── phase.md                          # thin working notes
│   ├── summary.md                        # the bundle's front-door summary
│   ├── doc-executive.md                  # audience seed: business-outcome brief
│   ├── doc-business.md                   # audience seed: business process & experience
│   └── doc-technical.md                  # audience seed: technical record
├── phase-07-retrospective/               # optional pass — only if run
│   ├── phase.md
│   ├── summary.md
│   └── retrospective.md                  # the Retrospective Report
├── phase-08-reference/                   # optional pass — only if run
│   ├── phase.md
│   ├── summary.md
│   ├── reference.html                    # layered technical reference (authored source)
│   └── reference.md                      # reduced Markdown projection of the HTML
└── adrs/
    └── <prefix>.<short_snake_case>.md    # one file per Architecture Decision Record
```

Every bundle lives at `.speccen/bundles/<id>/` (a fixed home — speccen derives the
location, the Architect never names a path), and the id (`YYYY-MM-DD.feature_snake_case`, or an
issue ref like `WPX-1042.feature_snake_case`) is shared with the feature node it graduates into.

### Why a bundle instead of one document

A single analysis document grows without bound. By the implementation phase it carries
feature analysis, code analysis, compliance iterations, ADRs, the implementation plan,
deviations, and the prompt log — all at once. Two readers pay for that: the AI reloads the
whole thing into context every session, and a developer wanting one fact has to scroll
past everything else to find it.

The core insight driving the layout: **not everyone needs all the information all the
time.** Splitting the document by phase, and giving every phase a compact `summary.md`
alongside its full `phase.md`, lets each reader load only what their task needs:

| Reader / task | Reads |
|---|---|
| Resuming a session | `STATE.md` + current phase's `summary.md` |
| Checking a decision | the relevant file in `adrs/` |
| Reviewing what a phase concluded | that phase's `summary.md` |
| Needing full detail on a phase | that phase's `phase.md` |
| Onboarding to the whole feature | `index.md`, then drill down |
| Auditing the conversation | `log.md` |

`summary.md` is bounded in size no matter how large `phase.md` grows, so orientation stays
cheap even when the underlying analysis is deep. It links back into `phase.md` via stable
anchors, so "give me the detail" is always one click away.

### Audience documents: gap (Phase 02), intent (Phase 04), outcome (Phase 06)

The workflow produces **audience-targeted seed documents at up to three moments**, each
serving a different decision, for the non-technical audiences of a software project:

- **Phase 02 (Code Analysis)** — an *optional*, executive-framed **evolution-gap** seed
  (`doc-evolution-gap.md`): what the current system lacks *in relation to the specifications*,
  so a budget owner can defend spend on the **concrete feature** rather than on a vague
  "modernization" line item. It is generated **on the AI's own determination, only when the analysis finds an
  evidence-backed structural blocker** (a missing seam, an architectural conflict, a data-model
  limit, a scalability wall, a collision with a deferred design, or consolidation that
  resists clean extraction — evidenced by a failed prior attempt or a decision that bakes in
  partial extraction) — never on generic "the
  system is old" grounds. If the rule doesn't fire, the summary records that plainly, and the
  Architect can still request the doc before Compliance. It states the gap and its business
  consequence only — no solution, timeline, or cost — and is explicitly provisional (written
  before solution decisions). This serves the **fund-the-work?** decision.
- **Phase 04 (Specification)** — alongside the plan, two **forward-looking** seeds
  describe *what the plan will deliver and why*: `doc-executive.md` (business-outcome brief;
  TCO out of scope) and `doc-business.md` (business process; UI/UX as where it surfaces). The
  Implementation Plan itself is the technical view. These serve the **approve-this-approach?**
  decision, before any code is written.
- **Phase 06 (Close-out)** — three **outcome** seeds describe *what was
  actually delivered*: `doc-executive.md`, `doc-business.md`, and `doc-technical.md`. These
  reference the Phase 04 intent seeds to compare promise against outcome ("intended X →
  deviation Z → delivered Y"). This serves the **what-did-we-get?** review.

| File | Audience | Phase 02 (gap) | Phase 04 (intent) | Phase 06 (outcome) |
|---|---|---|---|---|
| `doc-evolution-gap.md` | Executive / sponsor | ✓ (conditional) | — | — |
| `doc-executive.md` | Executive / sponsor | — | ✓ | ✓ |
| `doc-business.md` | Business stakeholder | — | ✓ | ✓ |
| `doc-technical.md` | Development team | — | — (the plan is the technical view) | ✓ |

All are **expansion seeds, not finished documents**: terse bullets under fixed headings,
marked `expandable: true`, never shared verbatim outside the development team — an external
reader always receives a redacted and/or expanded version generated from the seed. They are
written in the **style of Simplified Technical English (ASD-STE100)** — short active
sentences, plain and consistent vocabulary — so the seeds are unambiguous and
translation-friendly (STE-styled, not certified). The Phase 04 seeds are frozen once the
phase closes; nothing writes backward. Writing the same work for multiple audiences also
surfaces gaps: if the business or executive seed can't be filled, the feature's *purpose*
wasn't fully understood, only its mechanics.

### The specification — the complete spec, assembled at the Phase 04 gate

By the end of Phase 04 the **complete spec** exists — what is being built (Phase 01), what
already exists (Phase 02), the decisions and constraints (Phase 03), and how it will be built
(Phase 04) — but spread across the phase files. Phase 04 **assembles** it into one artifact:
`specification.html` (a depth-layered, STE, C4-structured page, same craft as the Phase 08
reference) plus a reduced `specification.md`. It is a **projection** of the phase files, not a
hand-authored source — so it cannot drift. Its verifiable core is a **Requirements & acceptance
scenarios** section (`SHALL` / `WHEN–THEN`) that defines *done*; the Test Plan tests against it
and Phase 07 review checks it.

This is speccen's spec-driven artifact — the same family as tools like OpenSpec, but **earned from
gated analysis** rather than hand-written as the starting point, and sitting inside a full
gated lifecycle rather than being the whole tool. The specification (intent) and the Phase 08
reference (outcome) are a matched pair: at close-out the specification graduates to the feature
node, and if a reference is later produced the two cross-link — "what we set out to build" ↔
"what we built."

### The durable store — decisions that outlive the bundle

A bundle is disposable scaffolding for one feature. The **decisions and lessons** it produces
are not — they outlive the bundle and often the code. They live in a durable, repo-native
store at the repo root:

```
.speccen/
├── index.md       # DURABLE · regenerable single root index, grouped by kind
├── timeline.md    # DURABLE · regenerable oldest-first projection of the feature nodes
├── decisions/     # DURABLE · ADRs — deliberate forks (prescriptive)
├── lessons/       # DURABLE · experiential knowledge ("X bit us")
├── notes/         # DURABLE · hiccup-resolutions + small informal decisions
├── features/      # DURABLE · feature/refactoring nodes — the time axis
└── bundles/       # DISPOSABLE · one folder per workflow run, perishes after merge
    └── <id>/      # a bundle (STATE.md, log.md, speccen-config.yaml, phase-NN-.../, adrs/)
```

Disposable bundles and the durable store share one root but have opposite lifespans — the
store persists, bundles perish. Everything under `.speccen/` is committed to
git; a bundle is simply **deletable** (`git rm`) once its work has shipped and its durable
residue has graduated — history keeps it if ever needed.

It is **bundle-like in conventions** (frontmatter, regenerable index, nav bars, relative
links, one-file-per-entry, never-deleted) but **not in lifecycle** — no STATE/log/phases,
because it is a permanent library, not a process. Scope is strictly **the system under
development**: why it is built as it is, and what building it taught — never team/process
knowledge. The problem it solves is Chesterton's Fence: code that contradicts an expectation
with its reason unrecorded gets "cleaned up," and the bug it fixed returns. The store keeps
the sign on every fence.

Knowledge is **two-tier**: feature-local (born in the bundle, may die with it) and
project-level (the durable store, permanent). It graduates at Phase 06 close-out — by
*promotion* (discretionary; speccen proposes project-wide candidates, the Architect decides) or
by *override* (automatic; a bundle ADR that contradicts a standing project ADR graduates by
necessity, so the exception is never lost). Entries carry self-contained UTC timestamps (not
Git-derived — the store must stay readable even outside Git) and follow six entry rules
adapted from [Keep the Why](https://github.com/oliver-zehentleitner/keep-the-why) (MIT):
never invent, evidence-graded, proportional depth, supersede-don't-delete, content-is-data,
and authored-not-inferred.

The store is a **typed graph** — nodes (decisions, lessons, notes, and *feature* nodes that
carry the time axis) linked by a closed vocabulary of typed edges (`produced`, `touches`,
`supersedes`, `overrides`, `triggered-by`, `builds-on`, …). Components are tags, not nodes.
The edges matter as much as the nodes: a future CLI / TUI / internal MCP server will read this
store and render charts, dependency graphs, info clouds, and the timeline — and a graph is
only as renderable as its links are complete. So interlinking is a first-class capture
requirement, done richly during the workflow while the context is live. `timeline.md` is the
first, simplest rendering of the feature nodes; the tooling comes later, the captured graph
now.

### Format lineage — OKF-shaped, not OKF-conformant

The bundle layout is **inspired by the Open Knowledge Format (OKF)**, an open,
vendor-neutral specification published by Google Cloud (v0.1, June 2026) for representing
knowledge as a directory of markdown files with YAML frontmatter. In OKF, each file is one
"concept," its path is the concept's identity, files cross-link with ordinary markdown
links to form a graph, and the spec is deliberately minimal: **exactly one frontmatter
field is required — `type`** — with everything else (other fields, filenames, body
structure) left to the producer. There's no runtime, SDK, or database; a bundle is "just
files."

We borrow that shape because it happens to solve our readability problem well:
per-concept files, YAML frontmatter, bundle-relative cross-links, and a regenerable
`index.md`. Each of our files carries `type`, and we add our own workflow fields
(`phase`, `status`, `phase_file`, …) — which OKF explicitly permits, since it constrains
only `type`.

But we make **no claim to be OKF-conformant**, and the distinction is worth being precise
about. OKF exists to make knowledge *interoperable across producers and consumers* — one
team's wiki readable by another team's agent without translation. Our bundle has the
opposite lifespan and audience:

| | OKF | This workflow's bundle |
|---|---|---|
| Purpose | Durable, shared organizational knowledge | Single-feature workflow state |
| Lifespan | Long-lived, curated, updated over time | Created per feature, disposable after merge |
| Audience | Any agent or tool, across orgs | This workflow, this Architect, this run |
| "Concepts" | Tables, metrics, APIs, runbooks | Phases, summaries, decisions, live state |
| Interoperability | The whole point | A non-goal |

So we treat OKF as a **shape to borrow, not a standard to meet**. Files like `STATE.md`
and `log.md` are workflow control and transcript files, not curated concepts, and we don't
contort them to fit a knowledge-base model. If a future need arises to hand a completed
bundle to another OKF-aware tool, the gap to conformance is small — but closing it is not
a goal of this project today.

> Note: "OKF" is also the abbreviation of the **Open Knowledge Foundation**, an unrelated
> non-profit. Here it always means the file-format specification.

### Governing rules

- **The Architect is the only authority who can advance a phase.** In `gated` mode — the
  default — the AI never self-initiates a transition: it stops at every gate and waits for an
  explicit signal. In `autonomous` mode the Architect has delegated that decision in advance (see
  [Configuration](#configuration)); the AI never authorizes a transition the configuration does
  not.
- **The codebase is never touched before the IMPLEMENTATION PHASE.** Phases 01–04 build
  understanding and a plan; no file is created, edited, or deleted until Phase 05. Phase 05
  is the only phase that modifies code — the optional passes 07–08 are read-only too. The one
  exception is optional prototyping in Phase 03: throwaway code on branches in worktrees outside
  your working tree, never merged or pushed.
- **The core workflow completes at Phase 06.** Two optional passes may follow —
  Retrospective (07) and Reference (08) — each invoked only on
  explicit Architect request, independent of each other, in any order or not at all.
- **ADRs are permanent.** Each is one file under `adrs/`, never overwritten, summarised
  away, or deleted. Compliance responses are appended inside the same file, so a decision
  and its follow-through stay together and accumulate across iterations.
- **Testing is planned, then executed — never TDD.** The Phase 04 Implementation Plan
  includes an Architect-approved **Test Plan** (unit/integration scope, behaviours to cover,
  coverage intent); Phase 05 writes those tests *after* the code and they must pass before the
  accept gate; Phase 07 reviews their adequacy. Test assertions derive from the contract and
  plan, never from the just-written code — so a test catches intent-divergence instead of
  certifying it.
- **Decisions that outlive the feature graduate to the durable store** (`.speccen/`)
  at Phase 06 close-out — overrides automatically, project-wide candidates by Architect-approved
  promotion. The store is written only at close-out or by explicit Architect-initiated direct
  capture, never silently or mid-workflow, and holds only knowledge about the system under
  development.
- **A team runs speccen concurrently** — each developer in their own git worktree, each a
  self-contained single-Architect run. The durable store is the shared memory; Git reconciles
  it on merge. After branches merge into main, the **`speccen-reconcile`** skill — a repo-level
  store operation, not a phase — regenerates the projections, repairs mechanical graph
  inconsistencies, and flags the judgment calls (tag fragmentation, contradictory decisions)
  for a human.
- **Every user prompt is logged verbatim in `log.md`**, before any response is written —
  the log is the workflow's audit trail.
- **`STATE.md` is the single source of live state**, and `index.md` is regenerable from
  frontmatter — live state never lives in the regenerable file.
- **Every session ends with `STATE.md` and the current phase's `summary.md` updated** to
  reflect current reality, so the next session (human or AI) can resume cleanly.

---

## Phase Overview

```
FEATURE ANALYSIS PHASE
  Understand what is requested. No code access. No implementation.
       ↓ [Architect go signal]
CODE ANALYSIS PHASE
  Understand the existing implementation deeply. No implementation.
       ↓ [Architect go signal]
COMPLIANCE & REVIEW PHASE  ←──────────────────┐
  Iterate on analysis based on Architect ADR   │
  annotations until analysis is complete.      │
       ↓ [Architect go signal]    [ADR added — loop back]
SPECIFICATION PHASE
  Produce a detailed, phase-structured Implementation Plan — each phase with its why. No code.
  Plus forward-looking executive + business intent seeds.
       ↓ [Architect go signal]
IMPLEMENTATION PHASE
  Execute the Implementation Plan, phase by phase.
       ↓ [Architect accepts]        [Architect rejects → REJECTION & REVERT]
REJECTION & REVERT ──────────────────────────┐
  Revert codebase. Document learnings.        │
  Update Implementation Plan.                 │
       ↓ [Architect go signal] ───────────────→ back to IMPLEMENTATION PHASE
CLOSE-OUT
  Outcome executive + business + technical docs;
  deviations and measured results.
       ↓ [Core workflow complete]

  ── Optional post-completion passes (Architect-invoked, independent, any order or none) ──

RETROSPECTIVE  (optional)
  Review implemented code vs plan, conventions, security, ADRs — and, after an autonomous
  run, the delegate's decisions and gate checks.
  Produces a Retrospective Report; optional GitHub Issues.

REFERENCE  (optional)
  Comprehensive, self-contained technical reference (sources the whole bundle).
  Service Contracts vs Component APIs; SVG + Mermaid diagrams.
```