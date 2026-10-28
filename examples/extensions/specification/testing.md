---
type: Extension
status: active
owner: team-leads
since: 2026-10-27
why: Unit tests alone missed integration and layering regressions in recent releases.
---

# Testing — required kinds of tests in the Test Plan

## What

Besides unit tests, the Test Plan of every change includes:

- an **integration test** for each changed endpoint or repository, against a real database
  (Testcontainers);
- an **architecture test** for each layering rule the change touches (NetArchTest);
- for a **fix**: a **regression test** that fails before the fix and passes after it.

## How

- In the Test Plan (Step 5 — Write the Implementation Plan), add one row per required test to the
  scope table, with its kind as the test type, tagged `[ext: testing]`.
- A kind that does not apply — no endpoint changed, no layering rule touched, not a fix — gets a
  row marked "not applicable, because …".
- Add to the Quality Criteria: every acceptance scenario that touches an endpoint has an
  integration test.
