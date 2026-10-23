---
name: ux-review
description: "Senior UI/UX review of a mobile screen, flow, canvas artboard, or Flutter UI code, using a real screenshot where possible. Prioritized findings with concrete fixes."
argument-hint: "<screen | flow | file path | claude.ai artifact url>"
disable-model-invocation: true
---

# Senior UI/UX review

Review like a senior product designer, for a real person and not a style guide. Review only what the user points at. Do not edit anything unless asked.

## 1. Learn who it is for

Read the project's CLAUDE.md, GLOSSARY.md and any design plan (for example `.scratch/redesign/plan.md`) before judging. State in one line each: who uses it, on what device, doing what, how often. If you cannot tell, ask once.

## 2. Get the real thing

Look at the actual screen. Never review from a description alone. Use the best source available, and say which you used:

1. **Live screenshot (best).** Real build on a real device or emulator.
   - Android: `adb devices` to find the device, then `adb exec-out screencap -p > <file>.png`.
   - iOS simulator: `xcrun simctl io booted screenshot <file>.png`.
   - Ask the user to open the screen, or capture the current one. Ask before capturing if the screen may show personal data.
   - Save under `.scratch/ux-review/` if `.scratch/` is in `.gitignore`, else the OS temp dir. Never commit screenshots.
   - View the PNG with the Read tool.
2. **Design source.** The canvas artboard (Artifact `read`), or the spec for the screen.
3. **Code.** The widget file. It shows structure and values, not how the screen looks.

When both a design source and a live screenshot exist, compare them. The gaps between intended and shipped are the best findings.

Cite where each finding lives: screen name, or `file:line`.

## 3. Redact

Screenshots can show customer names, phone numbers, amounts tied to a person, and notifications. Do not repeat them in the report. Write `<REDACTED>` or a generic label such as "a customer".

## 4. Check, in this order

1. **Core task speed.** Can the main job be done in few taps, one-handed? What does the eye land on first?
2. **Clarity.** Labels a non-technical user understands, one clear primary action per screen, nothing that looks tappable but is not.
3. **States.** Empty, loading, error, offline, partial data, very long text, very large numbers. A missing value is never shown as zero.
4. **Forms and money.** Optional vs required, sensible keyboard type, unambiguous units and currency, destructive actions confirmed or undoable.
5. **Khmer text.** Line height leaves room for stacked vowels and subscripts, nothing clipped in fixed-height boxes, Khmer and Latin align, text follows the system font size.
6. **Touch and access.** Targets at least 48dp, space between them, text contrast at least 4.5:1, meaning not carried by colour alone, Semantics labels on icon-only buttons.
7. **Consistency.** Spacing, radius, colour and type come from the theme or tokens, not literals. The same job uses the same pattern on every screen.
8. **Flutter fit** (code only). Theme and tokens, `const` widgets, no fixed sizes that break on small phones, dark mode, `textScaler`.

## 5. Report

At most 10 findings, most important first. Each has:

- **What** in one sentence, and **where**.
- **Why it hurts this user**, in plain words.
- **Fix**: concrete (a value, a widget, or the copy), small enough to do today.
- A tag: **High** (blocks the task), **Medium** (slows or confuses), **Low** (polish).

Say what already works in one line. Then list the top 3 things to do first.

## Rules

- Evidence over taste. Mark a preference as a preference.
- Do not invent usage data or quotes. If usage is unknown, call the finding untested and suggest the cheapest test, such as watching one real person do the main task.
- Respect decisions already settled in the design plan unless they cause a real problem. If they do, say which and why.
- If other skills add closing lines, use the shared ending format.

## Ending

Last lines, in this order, skipping any that are empty:

```
Verified: <what was seen> (<source: screenshot, artboard, or code>)
Not verified: <what> (<why>). Run: <command>
Skipped / risks: <one line>
Next: <one action doable in under two minutes>
```
