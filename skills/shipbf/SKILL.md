---
name: shipbf
description: '"Can we ship this by Friday?" A yes / no / yes-if verdict on whether the current work fits in a budget of agent sessions, computed from the measured pace of this branch, not from optimism. Friday is not a calendar day: the unit is the agent session. Use when the user asks can we ship this by Friday, will this be done by X, how much is left, are we close, how many more sessions, or runs /shipbf [N]. Depends on the hawd checklist. For plain status use hawd or wawat.'
---

# shipbf: can we ship this by Friday?

The question is slop. The answer is not. This is the one skill in the set
that gives a judgment, so it is the one most tempted to please. The rules at
the bottom exist to stop that.

## Friday in agent time

A person measures work in days. An agent measures it in sessions: one focused
window of context, roughly what a person calls "a sitting". So:

- **Unit:** the session.
- **Default Friday:** the end of the current session. `/shipbf` with no
  argument asks "does the rest fit in this session?".
- **`/shipbf N`:** does the rest fit in N sessions?
- The user's own config may define a session-to-day ratio. If they say
  "a Friday is three sessions", use that and say you did.

## Measure, do not guess

1. **What is left.** Run the `hawd` recipe and take its Next checklist. Count
   items. Note how many are `(inferred)`.

2. **How fast this branch has moved.** Sessions are bursts of commits. A gap
   longer than 90 minutes between commits ends a session.
   ```bash
   base=$(git remote show origin 2>/dev/null | sed -n 's/.*HEAD branch: //p'); base=${base:-main}
   git log --format='%at' "$base"..HEAD | sort -n | awk '
     NR==1 { s=1; prev=$1; next }
     { if ($1 - prev > 5400) s++; prev=$1 }
     END { print s+0 }'
   ```
   Velocity = Done items divided by sessions so far. Done items are the ones
   `hawd` counted, not raw commits.

3. **Sessions needed** = Next items divided by velocity, rounded up. If
   velocity is zero or there is fewer than two sessions of history, there is
   no measurement, and the verdict is capped at "yes, if" (see rules).

4. **What the agent does not control.** Review turnaround, CI, a merge from
   someone else, a credential, a deploy window. These are human time. List
   them. They never enter the session count. They go in the caveat.

## Answer

```
Verdict: yes | no | yes, if

| remaining | done so far | sessions so far | velocity | sessions needed | budget |
|-----------|-------------|-----------------|----------|-----------------|--------|
| 4 items   | 6 items     | 2               | 3/session| 2               | 1      |

Yes, if we cut:
- <the Next items that do not fit, cheapest to cut first>

Not counted (human time): review on PR #12, CI is red, needs a prod credential
Measurement quality: <good | thin: N sessions of history | none: velocity unmeasured>, <k> of <n> Next items are inferred
```

Keep it under 20 lines. The table is the answer; the prose around it is short.

## Rules

- The verdict is at most "yes, if" when any of these hold:
  - fewer than two sessions of history on this branch,
  - every Next item is `(inferred)`,
  - there is no goal source (no plan, no PR body, no issue).
- "No" must name the number: "no, this needs 3 sessions and the budget is 1".
- Never round sessions needed down.
- When "yes, if" cuts something, cut items, not quality. Do not propose
  skipping tests as a cut unless the user's plan already lists tests as
  optional.
- Do not run the test suite to look thorough. `hawd` already decided whether
  tests were cheap to run.
- Reply in the language the user asked in. Keep "Verdict" and the table
  headers in English.
