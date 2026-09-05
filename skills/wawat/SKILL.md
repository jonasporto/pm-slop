---
name: wawat
description: '"Where are we at with X?" Find a named piece of work (feature, ticket, branch, PR, topic) across branches, PRs, issues, commits and docs, say which candidate you picked, and report its position: shipped, in progress, stalled, never started or abandoned, with the same Goal / Done / Next checklist as hawd. Use when the user asks where are we at with X, what happened to X, did X ship, is X done, status of X, or runs /wawat X. For the current checkout without a name use hawd. For a deadline question use shipbf.'
---

# wawat: where are we at with X?

The question is slop. The answer is not. `hawd` looks at what is in front of
you. `wawat` has to find X first, say what it found, and only then report.

If the user gave no X, ask for one in a single line and stop.

## Find X

Run all of these with X as the search term. Try the obvious variants once
(ticket id, kebab-case, the noun alone).

```bash
git fetch --quiet 2>/dev/null
git branch -a --list "*X*"
git log --all -i --grep="X" --date=short --format='%h %ad %D %s' | head -20
git log --all -i --date=short --format='%h %ad %D %s' -- "*X*" | head -20
gh pr list --state all --search "X" --limit 10 --json number,title,state,mergedAt,headRefName,updatedAt 2>/dev/null
gh issue list --state all --search "X" --limit 10 --json number,title,state,updatedAt 2>/dev/null
grep -ril "X" PLAN.md docs plans board .github 2>/dev/null | head
```

Then pick ONE candidate and say so in the first line of the answer:
`Looking at: branch feat/x (PR #12), 3 other matches ignored: ...`.
If several candidates are equally plausible, list them in one line each and
ask which. If nothing matches anywhere, answer `never started: nothing named
X in branches, PRs, issues or commits` and stop. Do not pad that answer.

## Position

Decide the state from evidence, in this order:

| state | evidence |
|---|---|
| shipped | PR merged, or the commits are reachable from the base branch |
| in progress | unmerged branch or open PR with a commit in the last 14 days |
| stalled | unmerged branch or open PR, last commit older than 14 days |
| abandoned | PR closed without merge, or branch deleted with no merge |
| never started | only an issue, a doc mention or nothing at all |

"14 days" is a default. If the repo's own cadence is obviously slower, say
what threshold you used.

## Report

For shipped: one line with the PR, the merge date and the tag or release that
contains it if you can find one (`git tag --contains <hash> | head -3`).
Then the Done checklist. No Next section.

For everything else: run the `hawd` recipe against the candidate branch
without checking it out. Replace `<base>..HEAD` with `<base>..<branch>` and
read the PR of that branch instead of the current one.

```
Looking at: <candidate> (<what else matched>)
State: <shipped | in progress | stalled since <date> | abandoned on <date> | never started>

Goal: ...  (source)
Last touched: <date> by <author>, <what>

Done
- [x] ...  (<hash>, <date>)

Next
- [ ] ...  (from: ...)
- [ ] ...  (inferred)

Could not verify: ...
```

## Rules

- Say which candidate you picked before saying anything about it.
- Same evidence rules as `hawd`: Done needs a hash, Next needs a source or the
  `(inferred)` label.
- "Stalled" is a date, not a feeling. Print the date.
- Do not check out or modify anything. This skill is read only.
