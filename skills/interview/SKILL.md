---
name: speccen-interview
description: Works through the open decisions of a speccen phase in strict rounds — a numbered list of clear questions, each with a proposed answer. In gated mode the Architect answers; in autonomous mode the delegate answers from the decision basis and records the same questions, so the decision tree stays traceable. Used in Phases 01, 03 and 04, for autonomous stops, and for the end-of-run review.
metadata:
  inspired-by: "grilling, by Matt Pocock"
  inspired-by-url: "https://github.com/mattpocock/skills/tree/main/skills/productivity/grilling"
  inspired-by-license: "MIT"
---

# speccen — Interview Skill

> File paths, frontmatter, anchors, the navigation bar and structured questions follow the
> governing skill (`skills/00-governing-workflow/SKILL.md`). `{{PHASE_FILE}}` is the calling
> phase's `phase.md`.

## Purpose

A phase often has decisions to settle before it can write its output. This skill works through
them in a strict rhythm of **rounds** — each round a numbered list of clear questions, each with a
proposed answer. In `gated` mode the Architect answers. In `autonomous` mode the delegate answers
from the decision basis and writes down the same questions and answers.

Either way the bundle keeps one record: what was asked, what was proposed, who decided, on what
basis, and what depended on what. That record is a **decision tree a reviewer can follow**.

## When it runs

- **Phase 01:** when the brief leaves something open (Phase 01, Step 2a). This is a
  **discovery** interview: "don't know yet" is a normal answer.
- **Phase 03:** for open questions and every undecided (`PENDING`) ADR, one question per ADR.
- **Phase 04:** for material choices the analysis and the ADRs leave open, before the plan is
  written.
- **Stops** in `autonomous` mode — see **Stop rounds**.
- **The review** after an `autonomous` run — see **The review round**.
- **On request:** whenever the Architect asks to be interviewed.

## The decision tree and the frontier

List the open decisions as a **tree**: each decision, and the decisions that depend on its answer.
The **frontier** is every open decision whose prerequisites are all settled — the questions that
can be asked now without guessing an answer that has not been given yet. Record, for every
question, the questions it depends on.

**Facts are yours; decisions are the Architect's.** Never ask the Architect for something you can
find out.
- A fact in the bundle or the durable store (an ADR, a standing decision, an earlier feature):
  look it up — inline, or as a read-only `speccen-worker` unit (governing skill → Subagent
  Execution).
- A fact in the code: Phase 01 does not read code, so mark the question "for Phase 02" and leave
  it out of the interview. Phases 03 and 04 may look it up in the code.
- A lookup in progress holds back only the questions that need its result. Put the other
  questions forward meanwhile.

## Numbering

Questions are numbered per phase, and the numbers run on across rounds — round two continues
where round one stopped. Refer to a question with its phase: `01/7`, `03/2`. Use that reference
everywhere — in `log.md`, in delegated decisions and in reviews — so every reference points at
exactly one question.

## The round

Every round follows the same five steps — no step is skipped and no step is merged:

1. **Recompute the frontier** from the answers so far.
2. **Put the whole frontier as a numbered list:**

   ```markdown
   **Round [n]**

   **01/7 — [Short title]** — [The question: one decision, stated plainly, with one line on why
   it matters. Name the options if there are any.]
   → Proposed: [the answer you propose]

   **01/8 — [Short title]** — [...]
   → Proposed: [...]
   ```

   - **One decision per question**, phrased so that a plain "yes" takes the proposed answer.
   - Answers come by number ("7 yes, 8: use the queue"), or as one "yes" for the whole round.
   - This list is the interview's own format. It does not use the `AskUserQuestion` tool.
3. **Answer.**
   - `gated`: wait for the Architect. Never answer for them and never continue on an assumed
     answer.
   - `autonomous`: answer each question yourself from the decision basis (governing skill →
     Autonomous Execution). A question the basis decides, or a non-material one it leaves open,
     you answer — it is a delegated decision. A material question the basis does not decide goes
     into a **stop round**. In Phase 01, a question Phase 02 can investigate becomes a working
     hypothesis.
4. **Record.**
   - `log.md`: every answer — the Architect's verbatim (Invariant 5), the delegate's with its
     question reference.
   - `{{PHASE_FILE}}`, under `<a id="interview"></a> ## Interview`, one table per round:
     `# | question | depends on | proposed | answer`. The **answer** cell always says who
     answered:
     - `Architect: "<their words>"`
     - `delegate: <answer> — basis: <sources> — DD-<n>`
     - `hypothesis: <assumption> — settles in Phase 0<n>`
     - `later — parked` or `later — open item for Phase 0<n>`
   - Every question the delegate answers is a **delegated decision**: write a `DD-<n>` entry
     with the question reference, and link the DD from the answer cell.
5. **Move the frontier.** Each settled decision frees the decisions that hang on it. Hold back
   any question whose prerequisite is still unanswered within the current round; it comes up in
   a later round.

### Answers that are not a choice

- **"Later"** — the question and everything that depends on it become open items
  (`open-items` anchor). In a stop round, see **Stop rounds**.
- **"Don't know yet"** (discovery, Phase 01) — record the proposed answer as a working
  hypothesis, with what would confirm or refute it, and an open item for the phase that settles
  it.
- **"Stop"** — the interview ends; the remaining frontier becomes open items.
- **"You decide"** — the Architect takes your proposed answer for that question. Record it as
  exactly that, with their words.
- **A question back** — answer it, then ask the question again.

## Stop rounds (`autonomous`)

When the delegate meets material questions that the decision basis does not decide:

- **Collect them.** Carry on only with work that does not depend on them. Put them to the
  Architect together, as one round — at the latest when nothing independent is left, or at the
  phase's gate test — each with your proposed answer.
- **The run waits** for the answers: "yes", "no, use X", or "later".
- **"Later" parks a question that does not block the rest of the phase**: the run carries on,
  but the phase's gate test cannot pass while a parked question of that phase is unanswered. A
  question that blocks stays a stop. In Phase 01, "later" makes a working hypothesis instead.

## The review round (after an `autonomous` run)

When an `autonomous` run ends, or whenever the Architect asks to review it, put one interview to
the Architect:

- **One question per delegated decision** (`DD-<n>`): `adopt`, `override` or `later`, with your
  proposed answer.
- **One question per pending graduation candidate**: `promote`, `keep local` or `later`, with
  your proposed answer and its evidence.

Record what the Architect states: set `ADOPTED` or `OVERRIDDEN` on the decision, and promote the
candidates they approve (Phase 06, Step 6 write rules). An override still needs the Architect's
ADR, which can be scribed on request (governing skill → Scribing a stated decision). "Later"
leaves a decision `DELEGATED` and a candidate pending. You record; you never choose a status or a
promotion.

## When the interview is done

The interview ends once no open decision remains on the tree — each branch has been worked
through and nothing rests on an assumption. In `gated` mode, then:

1. Give a short closing summary of the decisions — in the Architect's words where possible.
2. Ask the calling phase's closing question:
   - Phase 01: **"Is this where we stand? — yes / not yet"**.
   - Phase 03, with undecided ADRs: **"Record these decisions into their ADRs? — yes / not
     yet"**. That "yes" is the explicit request the governing skill's **Scribing a stated
     decision** needs.
   - Otherwise: **"Is this our shared understanding? — yes / not yet"**.
3. On "not yet", ask what is wrong and run another round. On "yes", hand back to the calling
   phase, which writes its output.

In `autonomous` mode there is no closing question: the recorded rounds stand, and the review
round puts them to the Architect afterwards.

## What this skill produces

- `log.md`: every answer, with its question reference.
- `{{PHASE_FILE}}`: the `## Interview` section (anchor `interview`), one table per round, showing
  dependencies and who answered.
- `delegated-decisions.md`: one `DD-<n>` per question the delegate answered, linked both ways.
- Open items, for every question answered "later" or left when the Architect stopped.

The Architect's answers are their own words, so they belong to the decision basis that later
`autonomous` phases rely on.

## What this skill does NOT do

- Does not ask the Architect for a fact it can find
- Does not ask a question while its prerequisites are open
- Does not choose a status or a promotion — it records what the Architect states
- Does not write the phase's analysis, ADRs or `STATE.md`
- Does not let a `gated` phase continue before its closing question is answered "yes"

## Quality Criteria

- [ ] Every round put the whole frontier, and only the frontier, as a numbered list
- [ ] Every question was one clear decision with a proposed answer
- [ ] Every question carries its phase reference (`01/7`) and its dependencies
- [ ] Every answer cell says who answered — Architect, delegate, hypothesis or later
- [ ] Every delegate answer is a `DD-<n>`, linked both ways
- [ ] Every "later" and every question left by a "stop" is an open item or a parked question
- [ ] `gated`: the closing question was answered "yes" before the calling phase continued
