# Update my skills

You are updating the skills in this folder (each skill is a subfolder with a `SKILL.md`).
Apply the changes below, one skill at a time.

## Rules

1. Before editing anything, copy the whole folder to a backup next to it (e.g. `skills-backup-<date>`) and tell me the path.
2. Do not touch `synced/`, `.trash/`, `diagram-design/`, `ponytail-debt/`, `ponytail-gain/`, `ponytail-help/`, `ponytail-review/`, `ponytail-audit/`, `setup-matt-pocock-skills/`, `domain-modeling/`, `grill-me/`, `grill-with-docs/`, `wait-what/`.
3. Keep each skill's YAML frontmatter valid. After all edits, parse every frontmatter and report any error.
4. Where a step gives exact text, use it. Where it says "rewrite", keep the meaning and the examples, and make the wording plain and shorter.
5. If an anchor text below is not found, stop and tell me instead of guessing.
6. At the end, show a short summary: one line per skill changed, and the before/after size of each SKILL.md.

## Shared ending format (used by several skills below)

When a reply ends after work was done, the last lines go in this order, skipping any that are empty:

```
Verified: <command> → <result>
Not verified: <what> (<why>). Run: <command>
Skipped / risks: <one line>
Next: <one action doable in under two minutes>
```

## 1. i-have-adhd-skill

1. Replace the `description` with:
   > Shape output for a reader with ADHD: lead with the next action, number multi-step work, restate progress, give concrete time estimates and a source for every number, and cut preambles and closers. Use for explanations, plans, debugging, multi-step coding work, and any reply longer than a few sentences.
2. Rewrite the "Exception: the action is performed in front of someone else" part of Rule 1 in plain language, max 4 sentences, keeping both Bad/Good examples.
3. Rewrite the "When the cause is two mechanisms interacting" part of Rule 8 in plain language, max 3 sentences, keeping the co-borrower example.
4. Add under Rule 3: "If other skills add closing lines, use the shared ending format: Verified, Not verified, Skipped / risks, then Next last."
5. Then print (do not save) a 6–8 line summary of this skill's core rules that I can paste into my always-on instructions.

## 2. ponytail

1. Replace the sentence "End your reply with one or two lines: what you skipped or did not check, and any risk the user must know." with:
   > End your reply with one line on what you skipped or did not check and any risk the user must know, placed just before the final "Next:" line if another skill adds one.
2. Add at the end of "The smallest complete change" list:
   > - If the `tdd` skill is active, follow its test-first order instead of adding the test afterwards.

## 3. task-observer

1. Add `disable-model-invocation: true` to the frontmatter.
2. In the `description`, delete everything from "IMPORTANT: invoke this skill before the FIRST tool call" to the end of the description. Add instead:
   > Run on request, for example at the end of a substantial session: "review this session for skill improvements".

## 4. grilling

Add after the paragraph that ends "Then wait for the user's answers before the next round.":

> Order each round's questions by importance, most consequential first.

## 5. to-spec

Replace "This list of user stories should be extremely extensive and cover all aspects of the feature." with:

> One story per distinct behavior, including edge cases and error cases. Do not split a single screen or action into several stories.

## 6. to-tickets

Add to the `<vertical-slice-rules>` list:

> - If the work spans more than one repository, say in each ticket which repository each part belongs to; prefer tickets that change one repository.

## 7. implement

Replace the whole body (everything after the frontmatter) with:

```markdown
Implement ONE ticket (or a small spec) per run. If given several, do the first unblocked one and stop.

1. If on main/master, create a feature branch first.
2. Call the Skill tool with "tdd" where possible, at pre-agreed seams.
3. Run the analyzer/typechecker and single test files regularly; the full test suite once at the end. Use the commands in CLAUDE.md / AGENTS.md if present.
4. Call the Skill tool with "ponytail-review". Then compare the diff to the ticket's acceptance criteria: list any criterion not met and any behavior added that the ticket did not ask for.
5. Fix critical findings. Apply refactors that remove duplication or fix unclear names, then re-run the tests. List remaining findings for the user.
6. Tick the ticket's acceptance criteria and set its status to resolved.
7. Commit to the feature branch.
8. End with the shared ending format: Verified, Not verified, Skipped / risks, Next.
```

## 8. tdd

1. Delete the paragraph that starts "When the shape of that interface is itself in question" (it points to a `codebase-design` skill that is not installed).
2. Add after "## What a good test is":
   > The examples in `tests.md` and `mocking.md` are TypeScript; apply the same ideas in the project's language. Where the project documents its own testing conventions (for example which dependencies to fake) in CLAUDE.md / AGENTS.md, those win over this skill.

## 9. code-review

Move the whole `code-review/` folder into `.trash/` (do not delete it). `implement` now uses `ponytail-review` plus an acceptance-criteria check instead.

## 10. diagnosing-bugs

1. Add right after the first line under `# Diagnosing Bugs`:
   > **Fast path.** If the error message and stack trace point directly at the cause (typo, null access at a named line, wrong import), fix it, add a regression test at the nearest seam, and skip phases 2–4. Use the full process when the cause isn't obvious or the first fix fails.
   >
   > Before building a loop, check CLAUDE.md / AGENTS.md / README for the project's test, run and log commands, and use those instead of guessing.
2. Replace the first paragraph of `## Redact` with:
   > Redact every secret (tokens, passwords, keys, auth headers) **and all personal data** (names, phone numbers, emails, addresses, account numbers, amounts tied to a person) before showing output or saving fixtures. Write `<REDACTED>` or a realistic fake in its place; fixtures need realistic fakes so they still exercise the code.
3. Replace loop item 2 with:
   > 2. **Request script** against a running service (curl, HTTP client, gRPC call), asserting on the response.
4. Replace loop item 4 with:
   > 4. **UI automation** for the platform: browser (Playwright, Puppeteer), mobile (Patrol, Maestro, Espresso, XCUITest), desktop (the framework's driver). Assert on visible state, logs or network.
5. Replace loop item 5 with:
   > 5. **Replay captured input.** For data whose format you don't control (webhooks, notifications, uploaded files, third-party API responses, messages from other systems), save real samples as test fixtures (redacted) and run them through the code path. When the external format changes, the fixture becomes the regression test.
6. Append to loop item 10:
   > On Windows without bash, write the same loop in PowerShell (`Read-Host` for prompts).
7. Add a new item after item 10:
   > 11. **Log stream filter.** Run the app and filter its log stream to the bug's signature (browser console, `adb logcat`, `xcrun simctl log stream`, server logs), so the symptom appears as one line.

## 11. verification-before-completion

1. Replace the sections `## Overview` and `## The Iron Law` with:

```markdown
## The rule

Don't claim work is done, fixed or passing until you have run a command that shows it, in this session, and read its output. Confident claims without evidence are the most common way agent work goes wrong.

## Match the evidence to the claim

- Small, local change: the relevant single test file or the analyzer.
- Before committing: the full unit test suite and the build.
- Slow suites (integration, e2e, device tests): before a release, or when the change touches that flow; not on every edit.

## When you can't verify

If a check can't run (no device, no network, missing credentials, sandbox limits), say so plainly instead of claiming success or stalling, using the shared ending format:

    Verified: `<command>` → <result>
    Not verified: <what> (<why>). Run: <command>
```

2. Rewrite `## The Gate Function`, `## Red Flags - STOP` and `## Rationalization Prevention` in a calm tone: no capitals for emphasis, no "lying", no "Iron Law". Keep their content. Merge the last two into one short list if they overlap.
3. Replace `## When To Apply` with:
   > Apply before saying work is done, fixed or passing, before committing or opening a PR, before moving to the next task, and after a sub-agent reports success. It does not apply to conversational statements that make no claim about the state of the work.
4. Keep `## Common Failures` and `## Key Patterns` unchanged.

## 12. handoff

Replace the first sentence ("Write a handoff document ... not the current workspace.") with:

```markdown
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
```

Keep the existing paragraphs about suggested skills, not duplicating artifacts, redaction, and arguments.

## 13. create-doc-md

1. Replace the `description` with:
   > Writes feature design docs and implementation records: decision-first, non-goals stated, alternatives recorded, every claim grounded in the actual codebase. Use whenever the user asks for a design doc, implementation doc, RFC, technical writeup, or to "document this feature", before building (plan) or after (record).
2. Create `create-doc-md/references/investigation.md` and move the entire `## Verify before you assert` section into it, unchanged.
3. Replace that section in `SKILL.md` with:

```markdown
## Verify before you assert

Every claim comes from code read in this session. When the doc analyzes existing behavior, a defect, or someone else's code, load `references/investigation.md` first and follow it.
```

4. In `## Done when`, add before the list:
   > For the **Small** tier, check only: grounded claims, shorter than the diff, one subject, no marketing words. The full list applies to Standard and Risky docs.

## Finish

1. Parse every changed frontmatter.
2. Print the summary from Rule 6.
3. Remind me that `setup-matt-pocock-skills`, `grilling`, `to-spec`, `to-tickets`, `implement`, `tdd` and `diagnosing-bugs` come from a third-party collection, so updating that collection would overwrite these edits.
