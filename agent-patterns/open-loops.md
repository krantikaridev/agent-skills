# Pattern: the open-loops ledger — nothing promised is ever silent

Agent work fails quietly: a ticket nobody owns, a promise with no date, a
"done" with no evidence. One CSV, checked mechanically, fixes all three.

## Schema

```
id, owner, next_action, check_by, status, link
```

- **owner** — one named agent or human, never a team.
- **next_action** — a verb with an artifact ("post the scrub PR", not "progress").
- **check_by** — a date. Empty is illegal; a linter fails the row.
- **status** — `open | waiting | done`. `done` requires evidence in the link or
  a trailing evidence field (a merged PR URL, a sent message ID, a test run).
  Marking done without evidence is a lint failure, not a judgment call.
- Every ticket created and every promise made in a commit adds its row **in the
  same commit**; closing a loop edits the same row.

## Daily checks (the part that makes it work)

A scheduled check — cron, CI job, or the first task of each agent run — asserts:

1. no row is `open` past `check_by` without an owner touch (past-due list);
2. no row is `done` without an evidence string;
3. no duplicate rows for the same loop;
4. every liveness job that must run daily has a check that **fails when it did
   not run** — silence is treated as failure, not as success. A stopped
   pipeline looks exactly like a quiet day unless you assert the count.

Exit codes: 0 pass, 1 fail, 2 blocked (missing input is blocked, never pass).

## Routing rule that feeds the ledger

Route work by confidence and consequence, cheapest capable agent first: ask
"can the cheapest free runner do this?" before giving it to an expensive one;
high confidence + low risk → act; medium → gather new evidence (a retry must
add information); money, sending, deleting, permissions → the human, with one
concrete question. Before dispatching to any runner, check it is actually live
in the registry of running agents — a stopped owner silently dropping work is
the classic outage here.
