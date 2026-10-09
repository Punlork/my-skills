---
name: verification-before-completion
description: Use when about to claim work is complete, fixed, or passing, before committing or creating PRs - requires running verification commands and confirming output before making any success claims; evidence before assertions always
---

# Verification Before Completion

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

## The gate

Before stating any status:

1. Identify the command that proves the claim.
2. Run the full command, fresh.
3. Read the whole output: exit code and failure count.
4. If it confirms the claim, state the claim with the evidence. If not, state the actual status with the evidence.

Skipping a step means the claim is a guess, not a check.

## Common Failures

| Claim | Requires | Not Sufficient |
|-------|----------|----------------|
| Tests pass | Test command output: 0 failures | Previous run, "should pass" |
| Linter clean | Linter output: 0 errors | Partial check, extrapolation |
| Build succeeds | Build command: exit 0 | Linter passing, logs look good |
| Bug fixed | Test original symptom: passes | Code changed, assumed fixed |
| Regression test works | Red-green cycle verified | Test passes once |
| Agent completed | VCS diff shows changes | Agent reports "success" |
| Requirements met | Line-by-line checklist | Tests passing |

## Warning signs

Stop and run the check when you notice any of these:

- Wording like "should", "probably" or "seems to" ("should work now" means run it).
- Saying "Great!" or "Done!" before the check has run.
- About to commit, push or open a PR without a check.
- Trusting a sub-agent's success report; check the diff yourself.
- Treating a partial check as the full one: a passing linter does not prove the build compiles.
- Feeling confident, tired or tempted to make an exception "just this once". Confidence is not evidence.
- Rephrasing a claim so this rule seems not to apply. It still applies.

## Key Patterns

**Tests:**
```
✅ [Run test command] [See: 34/34 pass] "All tests pass"
❌ "Should pass now" / "Looks correct"
```

**Regression tests (TDD Red-Green):**
```
✅ Write → Run (pass) → Revert fix → Run (MUST FAIL) → Restore → Run (pass)
❌ "I've written a regression test" (without red-green verification)
```

**Build:**
```
✅ [Run build] [See: exit 0] "Build passes"
❌ "Linter passed" (linter doesn't check compilation)
```

**Requirements:**
```
✅ Re-read plan → Create checklist → Verify each → Report gaps or completion
❌ "Tests pass, phase complete"
```

**Agent delegation:**
```
✅ Agent reports success → Check VCS diff → Verify changes → Report actual state
❌ Trust agent report
```

## When to apply

Apply before saying work is done, fixed or passing, before committing or opening a PR, before moving to the next task, and after a sub-agent reports success. It does not apply to conversational statements that make no claim about the state of the work.
