---
type: Extension
status: active
owner: team-leads
since: 2026-10-27
why: A regression test only proves a fix if it was seen failing first.
---

# Testing — running the required tests

## What

- For a **fix**, run the regression test before the fix is applied, and record that it fails.
- Run the integration tests against Testcontainers, never against a shared database.

## How

- In Step 4 — Write and run the planned tests: write the regression test first, run it, and record
  the failing run in `{{PHASE_FILE}}` before the fix; then apply the fix and run it again.
- Add to the Quality Criteria: for a fix, a failing run of the regression test is recorded before
  the fix.
