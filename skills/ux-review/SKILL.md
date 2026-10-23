---
name: ux-review
description: "Senior UI/UX review of a mobile app, screen, flow, canvas artboard, or Flutter UI code. Asks which lenses you want (fixes, design, flows), drives the live app, compares it to the design, and reports only those lenses, with evidence."
argument-hint: "[fixes|design|flows|all] <screen | flow | file path | artifact url>"
disable-model-invocation: true
---

# Senior UI/UX review

Review like a senior product designer, for a real person and not a style guide. Review what the user points at. Do not edit app code unless asked.

## 0. Choose the lenses

Three lenses answer three different questions. Run only the ones the user wants.

- **Fixes**: what is broken or confusing right now?
- **Design**: how should the existing screens look and work better (hierarchy, density, interaction depth, visual language)?
- **Flows**: what is missing that the user does outside the app?

If the first word of the argument is `fixes`, `design`, `flows` or `all`, use it and do not ask. Otherwise ask once with AskUserQuestion: one multi-select question, header "Lenses", three options in the order above, **Fixes (Recommended)** first. Choosing none, or "Other" with "all", means all three. If you cannot ask (no interactive session), run Fixes only and say so.

If no target was named and the conversation does not make it clear, ask once in plain text.

Later lenses may reuse earlier findings, but never repeat the same point under two lenses. Put it under the more specific one.

## 1. Learn who it is for

Read the project's CLAUDE.md, GLOSSARY.md, the design plan (for example `.scratch/redesign/plan.md`), its recorded decisions, and the issue file for each screen under review. They explain gaps that look like bugs and list ideas already settled or rejected. State in one line each: who uses it, on what device, doing what, how often. If you cannot tell, ask once.

## 2. Get the real thing

Never review from a description alone. Say which sources you used.

1. **Live app (best).**
   - Android: `adb devices`, then `adb exec-out screencap -p > <file>.png`. Drive the app with `adb shell input tap <x> <y>`, `input swipe`, `input keyevent 4` (back), and `input text` (Latin only, so Khmer typing is not tested).
   - iOS simulator: `xcrun simctl io booted screenshot <file>.png`.
   - Screenshot coordinates map to the device by the factor the image viewer states (displayed size to original size).
   - View each PNG with the Read tool, one screen at a time.
2. **Design source.** The artboards (Artifact `read`) or the screen spec.
3. **Code.** The widget file. It shows structure and values, not how the screen looks.

Compare design and live screen for every screen you capture. Read all the relevant artboards, not just the first two.

## 3. Walk the whole flow

Do the main job end to end, not one screen. Then trigger the states users hit: empty, validation error, wrong input, long text, back and cancel, and large system font if you can set it.

- Before creating data, ask. Use obviously fake data ("Test …"). Do not delete or edit existing data. List what you created at the end so the user can clean it up.
- Ask before capturing if a screen may show personal data. Never store credentials the user gives you, and never repeat them.
- Save screenshots in `.scratch/ux-review/<date>-<slug>/` and the report next to it, as `<date>-<slug>-review.md`. If `.scratch/` is not in `.gitignore`, say so in the report. Never stage or commit screenshots.
- Redact customer names, phone numbers, amounts tied to a person, and notifications in the report. Write `<REDACTED>` or a generic label.

## 4. Lens: Fixes

Check, in this order:

1. **Core task speed.** Taps and seconds to finish the main job, one-handed.
2. **Clarity.** Plain labels, one clear primary action per screen, nothing that looks tappable or editable but is not.
3. **States.** Empty, loading, error, offline, sync status, partial data, very long text, very large numbers. A missing value is never shown as zero.
4. **Forms and money.** Optional vs required, field order, keyboard type, where focus goes after a sheet closes, unambiguous units and currency, destructive actions confirmed or undoable.
5. **Navigation.** Every screen reachable the same way, back behaves.
6. **Language and text.** For Khmer: line height for stacked vowels, no clipping in fixed-height boxes, text follows system font size.
7. **Touch and access.** Targets at least 48dp, text contrast at least 4.5:1, meaning not carried by colour alone, Semantics labels, a large-text path.
8. **Consistency.** Spacing, radius, colour and type come from the theme or tokens.
9. **Flutter fit** (code only). Theme and tokens, `const` widgets, no fixed sizes that break small phones, dark mode, `textScaler`.

Report: High and Medium findings in full, most important first. Low findings as one short list at the end. Each full finding has: **what** in one sentence and **where** (screenshot filename, screen name, or `file:line`); **why it hurts this user**; a concrete **fix** small enough to do today; a tag **High** (blocks the task), **Medium** (slows or confuses), or **Low** (polish).

## 5. Lens: Design

How the existing screens should look and work, not what is broken. Look at:

1. **Hierarchy.** What leads the eye? Is solid emphasis (fill, weight, colour) spent on one thing per screen, or on many?
2. **Density and reading order.** Wasted columns, facts scattered across a card, unit and value far apart.
3. **Interaction depth.** Layers (page, sheet, keyboard) for the common case, and whether the common case can be inline.
4. **Noise.** Repeated or unneeded elements for this user (for example a second currency nobody uses, a status shown on every row of a list already filtered by that status).
5. **Pattern fit.** Inputs that are not inputs, sheets that leave empty space, destructive actions next to routine ones.
6. **Visual language.** Typography weights and sizes, colour roles, consistency across components.

Report: group by screen or theme. Each item has the **evidence** (screenshot filename or `file:line`), the **change**, **why**, and a tag **Observed** (visible in a screenshot or code) or **Opinion** (taste, to test with people). Respect the plan's tokens and recorded decisions; if a change conflicts with one, say which. End with one cheap test per group (for example a 5-second lookup test).

## 6. Lens: Flows

Reviewing what exists cannot find what is missing. Step back from the screens:

1. **Walk the user's real day**, in order. Mark each step that happens outside the app (paper, calculator, chat, memory). Each marked step is a candidate flow.
2. **Ask what they do twice.** A repeated calculation, copy-paste, lookup, or message is a candidate.
3. **Check what the app already stores or plans but never shows.**
4. **Check neighbouring tools** for the same job in the same market. Name a feature only if you can say why it fits this user.

Stay within the app's stated purpose. Do not propose a different product. Skip anything the plan's decisions already settled or rejected.

Before writing the report, show the ideas in the reply as a short list and ask the user which fit the shop, since they know it better than you do. Write the report with their verdicts: keep, drop with reason, or park.

Report: for each surviving idea, the **job** it serves, the **step in the user's day** it replaces, the **smallest version** worth building, and **how to test the need cheaply** (for example, ask three users). Always tagged **Hypothesis**. Rank flows below fixes and never let them crowd out a real defect.

## 7. Finish

The report file and the reply cover only the lenses that ran. Then add:

- What already works, in a few lines.
- The top 3 things to do first, across the lenses that ran.
- Test data you created, so the user can remove it.
- If fewer than three lenses ran, one closing line: "Also available: <the lenses not run>."

## Rules

- Evidence over taste. Mark a preference as a preference.
- Do not invent usage data or quotes. If usage is unknown, tag it Hypothesis and suggest the cheapest test.
- Estimates from screenshots (sizes, contrast) are labelled as estimates.
- If other skills add closing lines, use the shared ending format.

## Ending

Last lines, in this order, skipping any that are empty:

```
Verified: <what was seen> (<source: live screenshots, artboards, or code>)
Not verified: <what> (<why>). Run: <command>
Skipped / risks: <one line>
Next: <one action doable in under two minutes>
```
