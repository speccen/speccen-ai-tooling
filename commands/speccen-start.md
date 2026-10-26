---
description: Start (or resume) a speccen workflow — shortcut to the governing workflow skill.
argument-hint: "['<feature-name>' — optional; the workflow asks for anything missing]"
---

Start the speccen workflow.

Load the governing workflow skill, `speccen-governing-workflow`
(`skills/00-governing-workflow/SKILL.md` in this plugin), and follow its **Session Start
Protocol** from Step 1 — resolve the bundle via the structured question set, then proceed
under the governing skill's orchestration.

This command is only a shortcut: the governing skill is the single source of truth for how
the workflow starts, decides new-vs-resume, and runs. Do not duplicate or reinterpret its
logic here — defer to it entirely. If `$ARGUMENTS` contains a feature name, pass it through so
the Session Start Protocol can skip that question.
