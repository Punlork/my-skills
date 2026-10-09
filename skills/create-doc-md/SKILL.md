---
name: create-doc-md
description: >
  Writes feature design docs and implementation records: decision-first, non-goals stated, alternatives recorded, every claim grounded in the actual codebase. Use whenever the user asks for a design doc, implementation doc, RFC, technical writeup, or to "document this feature", before building (plan) or after (record).
disable-model-invocation: true
---

# Feature implementation docs

A feature doc exists so a competent engineer who wasn't in the room can understand what changed, why that shape and not another, and what to watch out for. Everything in this skill serves that. Anything that doesn't serve it is padding.

The failure mode to avoid is a doc that sounds authoritative and is quietly wrong. That happens when detail gets invented to fill a template. Prefer a short doc with three grounded sections over a complete-looking doc with plausible fiction in it.

## Before writing

Read the code. Every concrete claim in the doc — file paths, function names, table columns, endpoint shapes, current behavior — must come from something actually read in this session, not from what the codebase probably looks like. If a section can't be grounded, either go read more or write "TBD" and say so to the user.

Three things are needed and are usually missing from the request. Ask for whatever isn't inferable from the code or conversation, in one batch:

- What observably changes for whoever uses this — a user, a caller, an operator.
- What's deliberately out of scope. This is the highest-value question and users rarely volunteer it.
- Whether anything is risky: data migration, breaking change, auth surface, money, anything hard to reverse.

Don't ask about things the repo can answer. Checking `git log`, the test setup, and neighbouring modules is faster than asking, and it also reveals the conventions this doc should match.

## Size the doc to the feature

Docs bloat because a template gets applied uniformly. Match the shape to the work:

**Small** — one module, reversible, no interface change. Title, one-paragraph summary, approach, testing. Half a page. Skip the rest; a non-goals section on a two-file change is noise.

**Standard** — the default. Full template below, minus sections that would be empty.

**Risky** — migrations, breaking changes, anything touching auth or payments. Full template, plus rollout and rollback written as concrete steps someone could follow at 2am, not as intentions.

State which one is being used and why, so the user can push back.

A change that crosses layers, deletes an existing path, or alters a wire contract gets a written plan for review before the first file is edited. Scoping questions choose between options; they cannot surface the constraint nobody thought to ask about, and a plan read by the person who holds it can.

**Length budget.** The doc should be shorter than the diff it describes. Past ~120 lines, state the reason. Every house-style rule below pushes toward more — name the file, give the number, cite the line — and none of them pushes back, so applied together in good faith they compound into prose that is maximally verifiable and unreadable.

**One subject per doc, and the tier is chosen per document.** A risky change that happens to share a branch with routine ones is two docs, not one long one; Non-goals exists to say so. Picking the tier for the riskiest part and applying it to everything is how a 250-line change becomes a 253-line doc its own author can't read.

## Verify before you assert

Every claim comes from code read in this session. When the doc analyzes existing behavior, a defect, or someone else's code, load `references/investigation.md` first and follow it.

## Template

Include only sections with real content. An empty section is worse than an absent one.

```markdown
# <Feature name>

**Status:** Draft | In review | Implemented
**Author:** <name>
**Updated:** <YYYY-MM-DD>

## Summary
<Two or three sentences. What changes, for whom, and the shape of the approach.
Then the defining constraint: the one decision that makes this shape non-obvious
rather than the default anyone would have reached for. Write it as a plain
declarative sentence — never as a labelled aside ("The key thing is…", "Crucially…"),
which reads as filler and gets skipped. This is usually the most valuable line in
the doc. A reader who stops here should be able to describe the change to someone
else, including the part they would argue with.>

## Context
<The situation that makes this worth doing. Current behavior, and what's wrong with it.
Link to the issue or thread instead of restating it.>

## How it works today
<Only for a doc that changes existing behaviour. Show the current mechanism —
the call path, the key shapes, the one function that decides — before saying what
changes. Decision-first ordering serves a reader who already holds the system in
their head and silently fails for one who doesn't, and the second reader is who
the doc is for. Omit this section for a greenfield feature, where there is no
prior mechanism to establish.>

## Goals
<Observable outcomes, each checkable by the reader without reading the diff — a
signal in the running system, a number that moves, a behaviour they can trigger.
"An expired token gets a 401 instead of a 500" passes; "the session logic is
refactored" is a compliance check on the implementation wearing a goal's name.>

## Non-goals
<What this deliberately doesn't do, and briefly why. This is the section that prevents
scope arguments in review, so write it even when it feels obvious.>

## Approach
<How it works. Name real files, functions, tables, endpoints. Code blocks and small
tables where they're clearer than prose. Enough that someone could implement it, not
so much that it duplicates the diff.
Every mechanism states the quantity that bounds its value — duration, size, window,
per-call limit — and a mechanism whose line can't be written is the finding. A
user-visible timing mechanism (progress, cancel, debounce) gives each phase's
order-of-magnitude duration and whether a person could perceive it. A batching or fan-in
change lists each per-call limit (timeout, retry, payload, transaction) and says whether
it is now per-batch.>

## Alternatives considered
<Each option that was genuinely on the table, and the specific reason it lost. "Simpler"
isn't a reason; "requires a second round-trip per request" is. Omit this section rather
than inventing straw options.>

## Risks and open questions
<What could go wrong, and what's still undecided. Open questions belong here rather than
being silently resolved by guessing — but before an item lands here, run a resolution pass
over the material already in hand: files read this session, the project's authoritative
specs, anything the user has open. Only what survives that pass is a question. Then
classify what remains: a fact recoverable from an artefact nobody has read yet is a task,
not a blocker; a decision with no documented answer is the only thing actually blocked on
a human. Say where the answer was looked for, so the next reader doesn't re-escalate.>

## Testing
<What's covered and how. Name the test files. Note what isn't covered and why.>

## Rollout
<Only for risky changes. Ordered steps, flags, migration order, and how to roll back.>
```

## House style

These are what separate a doc that reads like good internal documentation from one that reads like generated filler.

**Lead with the answer.** Every section opens with its conclusion, then supports it. Background before decision makes the reader hunt. The exception is below: headings and the first line of Context and How it works today are problem-first.

**Sentence case for headings, and a heading names the situation.** "Alternatives considered", not "Alternatives Considered". A heading names the situation in domain words, never the chosen mechanism or a contrast with a rejected option — the rejected option means nothing to a reader not yet told what is being decided. The same holds for the first line of Context and How it works today; everywhere else is decision-first.

**One sentence per line in the source.** Markdown renders them as one paragraph, and diffs stay reviewable line by line. This matters if the doc lives in the repo.

**Present tense, active voice, and a subject.** "The handler validates the token" beats "the token is validated". Passive voice hides who does what, which is exactly the information a reader needs.

**Decisions are decided.** Write "requests are batched at 50" rather than "we will probably batch requests". Genuine uncertainty goes in open questions, not into hedged verbs scattered through the doc.

**No marketing adjectives.** Seamless, robust, powerful, elegant, comprehensive, simply, just. They add length and no information. Also drop "note that", "it's worth mentioning", and "in order to".

**Be specific where it costs nothing.** `POST /v1/invoices` beats "the invoice endpoint". `src/auth/session.ts:validateSession` beats "the session logic". Specificity is what makes a doc verifiable.

**Specificity has a ceiling.** Cite the file and line for a claim a reviewer will actually check. Don't cite one for a claim nobody disputes — a citation on an uncontested statement costs attention and buys nothing.

**Lead a defect with the domain story, not the identifiers.** "The organiser removes a guest, saves, reopens, and the guest is back" beats "`collectHiddenRowIds` enumerates `rowGroups`". An explanation built from the system's own identifiers describes the machinery and omits the consequence — verifiable, but not understandable. Name the mechanism immediately after, never instead.

**Show the sequence; don't name the parts.** When the reader must operate or defend a mechanism with several parties or steps — a design as much as a defect — give one case in time order: who holds what at each step, and where the caller's input is consumed, not only the parts' definitions. Two clarifying questions about the same mechanism mean the sequence is missing. Where a defect arises from two mechanisms interacting rather than from one being wrong, describing both accurately and joining them with "so" explains nothing — the gap is defined by what neither of them does, and nothing that lists what things *do* can convey that. Trace one concrete case through both: the state before, the input, what each mechanism does to it, the state after. A two-row before/after table is usually enough. The diagnostic to watch for in your own draft is a sentence joining two mechanism names with "so", "therefore" or "which means"; the causal word is doing work the reader has to reconstruct, and reconstructing it needs the understanding the sentence was supposed to supply.

**A doc its reader must defend needs the mechanism in plain words.** When the reader will carry the doc into a review, or use it to raise a defect in someone else's code, the decision still leads — but a one-paragraph statement of the mechanism goes with it, in their vocabulary, with no identifiers or spec references. The test: could they restate the problem to someone who pushes back? Accepting an analysis is not evidence that they can, and the signal that they can't is absent by construction, because a reader who can't restate it usually can't tell that they can't.

**Names in a proposed patch are part of the doc.** A change offered for review is read before it is run, and its identifiers are most of what gets read. Name locals after the domain entity they hold, not the algorithm they implement, and prefer the project's existing vocabulary over the more precise general term. Where a patch introduces more than one new identifier, define them in a short table before the code block. Treat "I don't understand the naming" as a defect report against the patch, not a preference — an unreviewable fix does not land.

**Size each section to its evidence, not to the template.** Three alternatives where three were genuinely on the table; one where one was. Padding a thin section to match the shape of a rich one is how Alternatives fills with straw options and Risks fills with risks nobody has. An absent section is a fact about the change; a padded one is noise the reader has to disprove before they can trust the rest.

**Numbers over vibes, each with its source.** "Adds roughly 40ms at p99" beats "slightly slower". A number that justifies a decision carries its source: measured (and how), a limit read from configuration or code (and where), or "unmeasured" plus the cheapest way to measure it. "Unmeasured" is information, not hedging. Estimates of work about to be done stay concrete.

**Link text describes the destination.** "See the [rate limiting design](url)", never "click [here](url)".

**Tables for parallel structure, prose for reasoning.** Comparing three options across the same dimensions is a table. Explaining why one won is prose.

**Tag code fences with a language, and samples keep their shape.** ```ts, ```sql, ```bash. Serialized samples (JSON, YAML, rows) stay in canonical one-key-per-line form, and are exempt from the length budget. Shorten by dropping fields and marking the cut, never by packing lines; every JSON block parses.

**Define an acronym or internal name on first use,** unless it's already in the repo's shared vocabulary.

## Example

The difference is visible in a single section. Same feature, same facts.

**Weak:**

> ## Approach
> We will be implementing a robust caching layer in order to significantly improve the
> performance of the user profile endpoint. Various caching strategies were considered
> and Redis was chosen as it is a powerful and widely-adopted solution. The cache will
> be invalidated when necessary.

Nothing here is checkable. No file, no key, no TTL, no invalidation trigger, and "when necessary" is doing a lot of hidden work.

**Strong:**

> ## Approach
> `GET /v1/users/:id` reads from Redis before falling back to Postgres.
>
> `src/users/repository.ts` gains a `findByIdCached` method wrapping the existing
> `findById`. Keys are `user:v1:<id>` with a 5-minute TTL. The `v1` segment lets us
> invalidate the whole namespace by bumping it if the serialized shape changes.
>
> Writes through `updateUser` delete the key rather than rewriting it, so a failed
> write can't leave a stale entry behind.
>
> On a Redis timeout the read falls through to Postgres and increments
> `cache.errors`. Availability doesn't depend on the cache.

Every claim points at something a reviewer can check, and the two decisions worth arguing about — delete-not-update, fail-open — are visible instead of buried.

## Done when

Every line below is a property of the draft, checkable against the draft. An item you cannot settle by pointing at something has failed, not passed — the whole point of this list is that recalling your own good intentions is not how it gets answered.

For the **Small** tier, check only: grounded claims, shorter than the diff, one subject, no marketing words. The full list applies to Standard and Risky docs.

- The doc is shorter than the diff it describes, or says why it isn't. `git diff --stat` against `wc -l`.
- The doc has one subject, and the size tier was chosen for this document rather than for the branch it happens to share.
- The Summary states the defining constraint as a plain sentence, with no labelled aside.
- Every Goal is checkable by the reader without reading the diff.
- Every claim about what runs, how many there are, who owns it, or what the contract says names the check it came from — a grep, a blame, a dated contract, a dispatch site.
- Every `file:line` citation was run as a count, and the count is in the doc.
- Nothing sits in Risks or open questions that the material already in hand answers.
- Every section either carries real content or is absent; none was padded to fill the template.
- Every defect explanation leads with the domain story, and every mechanism with several parties or steps is shown as one case in time order.
- No sentence joins two mechanism names with "so", "therefore" or "which means" and stops there.
- No heading names a mechanism or a rejected option.
- Every number that justifies a decision names its source: how it was measured, where the limit was read, or "unmeasured".
- No marketing adjective survives — seamless, robust, powerful, elegant, comprehensive, simply, just — and no "note that" or "in order to".
- Every code fence carries a language tag, every JSON block parses, and no sample puts two keys on one line.
- The person who watched this work being done could read it once and retell it.

Where the doc specifies a scripted edit to an existing file, specify the assertion rather than the diagnostic: `assert s.count(anchor) == 1` before replacing, never a printed occurrence count. A precondition that is measured and not gated on guarantees exactly the failure it was measuring, because nobody reads the print before the next line runs.

## After writing

Say where the file was written and offer the obvious next step: adjust the size tier, fill a TBD, or open the questions with whoever can answer them. If the doc lives in the repo, match the location and naming of docs already there rather than inventing a new convention.