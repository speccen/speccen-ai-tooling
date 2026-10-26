# Examples

Reference artifacts that set the quality bar for what the workflow produces.

## `pump-safety-rules-refactor.html`

A worked example of a **Phase 08 Reference** — the layered,
designed HTML page that the `speccen-reference` skill produces.

It documents a fictional water-pump safety-rule refactor on a generic rule engine
(Specification + Remedy + FactBag). The subject is invented; the **structure and craft are
the point**.

**Imitate its structure and visual craft:**

- **Progressive disclosure** — the document descends from the highest level to the deepest,
  and a reader can stop at any layer with a true picture.
- **C4 layering** of the architecture diagrams — L1 context → L2 containers → L3 components →
  L4 runtime sequence, each a zoom-in of the last.
- **Delta-highlighting** — every structural diagram makes what *changed* stand out and mutes
  what stayed.
- **Argument-diagrams** — a diagram that exists to make one subtle point visible (here, that a
  crash and a real violation look identical except for one field).
- **The "why" on the page** — rejected-alternatives and verdict tables at the key decisions,
  not just links out.
- **Grounded header** — test counts, commit, "baselines changed."

**Do not imitate its prose voice.** This example was built outside the workflow and is **not**
written in Simplified Technical English (STE); it uses rhetorical questions and narrative
framing that STE forbids. Everything the workflow itself produces follows STE (see the
STE-style rules in `skills/00-governing-workflow/SKILL.md`). The example is the bar for
*layout and depth*, not for *sentences*.
