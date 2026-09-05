---
name: hawd
description: '"How are we doing on this?" Status of the work in the current checkout: the goal, what is happening right now, a checklist of what is done (every item backed by a commit or file) and a checklist of what is next (every item backed by a plan, PR body, review comment, failing check or TODO, or labeled inferred). Use when the user asks how are we doing, how is it going, status, any updates, where do we stand, quick update, or runs /hawd. For a named thing that may not be in the current checkout use wawat. For a deadline question use shipbf.'
---

# hawd: how are we doing on this?

The question is slop. The answer is not. Every line you write is either
backed by something in the repo or labeled as inferred. Never invent green.

"This" means what is in front of you: the current checkout, its branch, its
dirty files, its open PR. Do not search for anything else. If the user names
a thing that is not this checkout, hand off to `wawat`.

## Gather evidence

Run these. Do not skip a step because the answer "seems obvious".

1. **Context**
   ```bash
   git rev-parse --show-toplevel && git branch --show-current
   git remote show origin 2>/dev/null | sed -n 's/.*HEAD branch: //p'   # base branch
   git status --short
   ```
   Base branch: the remote HEAD if there is one, else `main`, else `master`.
   If this is not a git repo, say so in one line and stop.

2. **Goal.** Search in this order and stop at the first hit:
   1. A plan or spec doc in the repo that names this branch or feature
      (`PLAN.md`, `docs/`, `board/`, `plans/`, `.github/`, anything the user's
      CLAUDE.md or AGENTS.md points to).
   2. The open PR body: `gh pr view --json title,body,url 2>/dev/null`.
   3. A linked issue: `gh issue view <n>` for any `#n` in the PR body or branch name.
   4. The branch description: `git config branch.$(git branch --show-current).description`.
   5. The branch name and its ticket id, if any.
   6. The first commit on the branch: `git log --reverse --format=%B <base>..HEAD | head -20`.
   If nothing above states a goal, write "Goal: not written anywhere" and
   propose one sentence from what was done, marked `(inferred)`.

3. **Done.** Commits on the branch since the base, oldest first:
   ```bash
   git log --reverse --date=short --format='%h %ad %s' <base>..HEAD
   ```
   Group commits into items a person would recognise as one thing. Keep the
   short hash of the last commit of each item. Staged or unstaged changes are
   not done: they go under "Doing now".

4. **Doing now.**
   ```bash
   git status --short
   git log -1 --format='%h %ar %s'
   gh pr view --json number,state,isDraft,reviewDecision,statusCheckRollup 2>/dev/null
   gh run list --branch "$(git branch --show-current)" --limit 3 2>/dev/null
   ```

5. **Next.** Real sources, in order. Cite which one each item came from:
   - Unchecked boxes in the plan doc or PR body.
   - Open review threads: `gh pr view --json reviews,comments`.
   - Failing or missing checks from step 4.
   - `TODO`, `FIXME`, `XXX` added by this branch:
     `git diff <base>..HEAD | grep -n '^+.*\(TODO\|FIXME\|XXX\)'`.
   - Failing tests, if a test command is obvious and cheap to run.
   If none of those exist, you may list what the diff obviously leaves
   unfinished, but every such item carries the label `(inferred)`.

## Answer

Keep it under about 25 lines. Dates are absolute. Hashes stay short.

```
Goal: <one or two sentences>  (source: PR #12 body | plan doc | inferred)

Doing now: <branch>, <n> dirty files, last commit <when>, PR #<n> <state>, checks <state>

Done
- [x] <item>  (<hash>, <date>)
- [x] <item>  (<hash>, <date>)

Next
- [ ] <item>  (from: PR body)
- [ ] <item>  (from: review by @x)
- [ ] <item>  (inferred)

Could not verify: <no gh | no CI | no remote | tests not run>
```

## Rules

- A Done item without a hash is not a Done item.
- A Next item without a source is `(inferred)`. Never hide the label.
- Do not reword the goal you found. Quote it, trim it, cite it.
- If the goal was inferred, offer once to write it into the branch
  description or PR body so the next `/hawd` has a source. Only write if the
  user says yes.
- Do not run the test suite if it looks slow. Say "tests not run" instead.
- Reply in the language the user asked in. Keep the checklist labels
  (Goal, Doing now, Done, Next) in English so they read the same everywhere.
