# pm slop

Answers to the questions PMs ask. The question is slop. The answer is not.

| you ask | you type | you get |
|---|---|---|
| how are we doing on this? | `/how-are-we-doing-on-this` or `/hawd` | goal, doing now, done checklist, next checklist |
| where are we at with X? | `/where-are-we-at-with-x X` or `/wawat X` | which X it found, its state, then the same checklist |
| can we ship this by Friday? | `/can-we-ship-this-by-friday [N]` or `/shipbf [N]` | yes / no / yes-if, from measured pace, in agent sessions |

The full name is the skill. The short one is an alias folder that points at
it, so both install and both trigger.

Every Done item carries a commit hash. Every Next item carries its source
(plan doc, PR body, review comment, failing check, TODO) or the label
`(inferred)`. The skills never invent green.

## Install

Works with Claude Code, Codex, Cursor and every other agent that reads
`SKILL.md` folders, in one command:

```bash
npx skills add jonasporto/pm-slop
```

Claude Code only, as a plugin:

```
/plugin marketplace add jonasporto/pm-slop
/plugin install pm-slop@pm-slop
```

The plugin route namespaces the commands (`/pm-slop:hawd`). The `npx skills`
route gives you the bare `/hawd`, `/wawat`, `/shipbf`.

## Friday in agent time

`shipbf` does not use calendar days. A person measures work in days; an agent
measures it in sessions, one focused window of context. A Friday is a budget
of sessions, default one (this session). `/shipbf 3` asks whether the rest
fits in three. Pace comes from the branch's own commit history, bursts of
commits split by 90 minute gaps, so the estimate is measured on this work,
not guessed.

## What it needs

`git`. With `gh` installed you also get PRs, issues, reviews and CI checks.
Without it the answer says what it could not verify.

Optional: if your repo keeps a plan doc, point your CLAUDE.md or AGENTS.md
at it and `how-are-we-doing-on-this` uses it as the goal source. Without one, the goal comes from
the PR body, the issue, the branch description or the first commit, in that
order, and the answer says which.

## Contributing

One skill is one PM question. The folder name is the question itself in
kebab-case, plus an alias folder with the acronym people would actually type. The description in the frontmatter lists the ways the question
is asked in real life, because that is what triggers the skill when nobody
types the slash.

Rules every skill follows:

- Evidence or label. No unlabeled inference.
- Read only. Ask before writing anything back into the repo.
- Short answer. The PM wanted one line; give the engineer twenty.

## License

MIT
