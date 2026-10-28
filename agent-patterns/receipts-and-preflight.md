# Pattern: receipts, restatements and preflight — making runs auditable

Three small disciplines that catch most agent failures: wrong task, unverifiable
claim, repeated wasted attempt.

## Restate before building

For any non-trivial ticket, the agent opens its output with 2–4 lines
restating the goal and the problem **in its own words** (not a copy of the
ticket). If the restatement does not match the request, it stops and flags the
mismatch instead of building. Catches misread tasks while they are still free.

## The receipt

Every run ends with a fixed-shape receipt a linter can read:

```
Inspected: <what was read/checked>
Changed:   <files/behavior, with the check that proves each>
Skipped:   <what was deliberately not touched, why>
Failed:    <what did not work, with the exact error>
Route:     <act | gather-more-evidence | escalate-to-human>
Evidence:  <test output, diff, screenshot path — fresh, from this run>
Waiting on: <the one question for the human, or nothing>
```

Plus two closing lines when you run a rules ledger: `Rules applied: R…` and
`Skills loaded: …` (or `Skill query: … none, because …`). A run with no receipt
counts as failed and is re-run — the check for this is mechanical: grep the log
for the field names.

## Verify with fresh evidence

Outcomes are verified by artifacts, not by "the command started": bytes copied,
test passed, screenshot rendered. The builder and the checker share one rubric,
written down, so PASS means the same thing to both. Verification runs **before**
features are declared, in the smallest checkable unit — see the
`verify-pattern` skill for the reusable shape (PASS/FAIL/BLOCKED, exit 0/1/2,
fail-closed on missing inputs).

## Confidence tags on claims

Any claim an agent writes carries one of four tags at write time:
`Sourced` (a link or file is given), `Verified` (a run/evidence is cited),
`Estimate` (an explicitly reasoned guess), `UNVERIFIED`. A figure is never
labelled sourced or verified without its source; counts are stated as lower
bounds. A reviewer spot-checks tags; an untagged claim fails review.

## Preflight and the repeat ledger

Before starting a task, run a preflight that answers: has this been done before,
and is there a canonical script? Keep a small ledger (`task, times done,
canonical script or skill, outcome`). Any task done 3+ times gets a canonical
script; stop after 3 identical failures and escalate — retrying without new
information is the most expensive agent habit.

Before asking a human anything, preflight memory and the config files first so
no known fact is escalated; ask one question with one exact action.
