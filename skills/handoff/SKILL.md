---
name: handoff
description: Compact the current conversation into a handoff document for another agent to pick up.
argument-hint: "What will the next session be used for?"
disable-model-invocation: true
---

Write a handoff document summarising the current conversation so a fresh agent can continue the work.

Save to `.scratch/handoffs/<YYYY-MM-DD>-<slug>.md` in the project (check that `.scratch/` is in `.gitignore`; if not, ask before adding it). Outside a project, use the OS temp dir. Always print the full path.

Use this structure; skip empty sections:

## Goal
## State: done / in progress / not started
## Decisions made (and why)
## Tried and failed (and why it failed)
## Verified / not verified
## Git: branch, uncommitted changes, last commit
## Open questions
## Next step (the first concrete action)
## Pointers (specs, tickets, ADRs, files)
## Suggested skills

End your reply with a line to paste into the new session:
"Read <path> and continue from 'Next step'."

Include a "suggested skills" section in the document, naming which skills the next agent should call the Skill tool for.

Do not duplicate content already captured in other artifacts (specs, plans, ADRs, issues, commits, diffs). Reference them by path or URL instead.

Redact any sensitive information, such as API keys, passwords, or personally identifiable information.

If the user passed arguments, treat them as a description of what the next session will focus on and tailor the doc accordingly.
