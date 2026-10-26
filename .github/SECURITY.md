# Security Policy

## Supported versions

This project uses calendar-based versions (`YY.MDD.N`). Security fixes are
applied to the latest release only. Please update to the most recent version
before reporting an issue.

## Reporting a vulnerability

Please do not report security vulnerabilities through public GitHub issues,
discussions, or pull requests.

Report them privately using GitHub's private vulnerability reporting:

1. Go to the [Security tab](https://github.com/speccen/speccen-ai-toolkit/security)
   of this repository.
2. Choose **Report a vulnerability**.
3. Describe the issue and, where possible, include the affected files or
   skills, steps to reproduce, and the potential impact.

If you cannot use GitHub's private reporting, email **speccen@cucurbit.be**
with the same details.

## What to expect

- We aim to acknowledge your report within 5 working days.
- We will keep you informed while we investigate and prepare a fix.
- Once a fix is released, we will credit you in the release notes unless you
  prefer to remain anonymous.

## Scope

This repository contains prompts, skills, agents, commands, and workflow
definitions for AI coding assistants. Examples of in-scope issues include:

- Instructions or commands that could cause an assistant to run unintended
  or destructive actions
- Prompt-injection weaknesses in the skills and workflows
- Vulnerabilities in the GitHub Actions workflows (for example, script
  injection or token misuse)
- Secrets or credentials committed to the repository
