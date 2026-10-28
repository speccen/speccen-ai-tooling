---
type: Extension
status: active
owner: team-leads
since: 2026-10-27
why: A required test can be present but hollow — named right, testing nothing.
---

# Testing — checking the required kinds

## What

- Every test the Test Plan tagged `[ext: testing]` exists and tests the behaviour it names, not a
  placeholder.
- A "not applicable" in the Test Plan really does not apply.

## How

- Add to the **Test adequacy** block of Step 3's review checklist: check each `[ext: testing]` test
  and each "not applicable" row.
- A hollow or missing required test is a finding with the prefix `[ext: testing]`.
