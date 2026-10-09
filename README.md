# my-skills

Find what you want to do, then type the command or just say it.

- **Type `/name`** means it only runs when you type it.
- **Just ask** means Claude starts it by itself when your request matches. You can also type `/name` to force it.

## Usually on (Claude starts these by itself; type `/name` if it didn't)

- **ponytail**: Claude writes the smallest code that solves the task.
- **i-have-adhd-skill**: answers start with what to do next, in numbered steps.
- **verification-before-completion**: Claude must run a check before it says "done".

## I want to…

| I want to… | Do this |
|---|---|
| Think through an idea before building | Say "grill me on this" |
| Same, and save the decisions as docs | `/grill-with-docs` |
| Turn our chat into a written spec | `/to-spec` |
| Split a spec into small tasks | `/to-tickets` |
| Build one task end to end | `/implement` |
| Write tests first, then code | Say "use TDD" |
| Write a design doc for a feature | `/create-doc-md` |
| Fix a bug I can't figure out | Say "debug this" |
| Get a change reviewed | Say "review this" |
| Check the whole repo for problems | Say "audit this repo" |
| See the shortcuts Claude left in code | Say "ponytail debt" |
| Draw a diagram | `/diagram-design` |
| Have the last answer explained again | `/wait-what` |
| Agree on the names of things in the project | Say "update the glossary" |
| Stop now and continue in a new chat | `/handoff`, then paste the line it gives you into the new chat |
| Improve my skills from this session | `/task-observer` |

`/to-spec`, `/to-tickets` and `/implement` need `/setup-matt-pocock-skills` run once in the project first.

Usual order for a new feature: grill → `/to-spec` → `/to-tickets` → `/implement` (one task at a time).

## Install changes

After editing `skills/`, copy them to Claude Code:

```sh
cp -R ~/.claude/skills ~/.claude/skills-backup-$(date +%F)
rsync -a --exclude .DS_Store --exclude .trash --exclude synced skills/ ~/.claude/skills/
```
