---
name: implement
description: "Implement a piece of work based on a spec or set of tickets."
disable-model-invocation: true
---

Implement ONE ticket (or a small spec) per run. If given several, do the first unblocked one and stop.

1. If on main/master, create a feature branch first.
2. Call the Skill tool with "tdd" where possible, at pre-agreed seams.
3. Run the analyzer/typechecker and single test files regularly; the full test suite once at the end. Use the commands in CLAUDE.md / AGENTS.md if present.
4. Call the Skill tool with "ponytail-review". Then compare the diff to the ticket's acceptance criteria: list any criterion not met and any behavior added that the ticket did not ask for.
5. Fix critical findings. Apply refactors that remove duplication or fix unclear names, then re-run the tests. List remaining findings for the user.
6. Tick the ticket's acceptance criteria and set its status to resolved.
7. Commit to the feature branch.
8. End with the shared ending format: Verified, Not verified, Skipped / risks, Next.
