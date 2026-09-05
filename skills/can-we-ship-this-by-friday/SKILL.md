---
name: can-we-ship-this-by-friday
description: '"Can we ship this by Friday?" A yes / no / yes-if verdict on whether the current work fits in a budget of agent sessions, from the measured pace of this branch. Friday is not a calendar day: the unit is the agent session. Use when the user asks can we ship this by Friday, will this be done, how much is left, are we close, or runs /shipbf [N].'
---

Can we ship this by Friday? Friday is in agent time: the unit is the session,
and the budget is N sessions (default 1, this one). From the current
checkout, work out:

1. What is left (the next steps, as in how-are-we-doing-on-this).
2. How fast this branch has moved: count sessions as bursts of commits
   separated by more than 90 minutes, then done items per session.
3. Sessions needed, rounded up, against the budget.

Answer yes, no, or yes-if, with the numbers in a small table. For yes-if,
list what to cut. List separately what is human time and not counted
(review, CI, someone else's merge). If there are fewer than two sessions of
history or every next step is inferred, the best you can say is yes-if.
