---
name: verify-pattern
description: "A reusable shape for any verification skill: one smallest checkable claim, a three-state verdict (PASS, FAIL, BLOCKED), fail-closed inputs, an evidence line and a hermetic test. Use when writing or reviewing a check that proves work happened instead of claiming it."
---

# Verify Pattern

A verification skill proves a claim with fresh evidence. This page is the shape every
such skill shares, so a new one (for applications, a learning drill, a release, a
sign-in) takes minutes and reads the same as the rest.

## The shape

1. **One claim, smallest unit.** Name the single thing that must be true ("every
   submitted application has a log row"). If a check needs "and", split it in two. Build
   and prove one unit before starting the next.
2. **Three verdicts, never two.**
   - `PASS` — the claim was checked against real inputs and holds.
   - `FAIL` — it was checked and does not hold; say which item and why.
   - `BLOCKED` — it could not be checked (missing input, signed-out session, human-only
     gate). Never report BLOCKED as PASS. State the one next step that unblocks it.
   Exit codes: 0 PASS, 1 FAIL, 2 BLOCKED, 3 usage.
3. **Fail closed.** A missing file, folder, session or tool is BLOCKED (or FAIL), with a
   clear message. Silence is never a pass.
4. **Evidence line.** Every verdict carries what was looked at: counts, ids, a path or a
   timestamp. "The command started" is not evidence; its result is.
5. **Joint checks.** When two checks each see half a failure (a worker idle, and work
   owed to that worker), add a check that joins them. It reuses both, adds no new source.
6. **Hermetic test.** Each check ships with a test on temp files: one PASS, one FAIL, one
   BLOCKED case. No network, no browser, no account.
7. **No secrets in output.** Codes, links, tokens and personal data are never printed;
   redact anything that looks like one.

## Output contract

```
FAIL idle-with-queue worker-a: ticket 001.md is 22 min old and worker is idle
INFO overdue owner-b: 2 item(s)
FAIL
```

Detail lines first, the verdict word alone on the last line, so a human reads the last
line and a script reads the exit code.

## Writing a new verify skill

1. Front matter: `name` (lowercase, hyphens) and a quoted `description` that says what it
   proves and when to use it.
2. A two-line intro: what a pass means, what a fail costs.
3. A `Config` section for anything personal, as `<placeholders>` filled from a private
   config file — the skill itself names no account or person.
4. One `## Check N` per claim: the command, then **Pass:**, **Fail:**, and **Blocked:**.
5. `## Overall`: all checks pass, or the run is not verified.

## Review checklist

- Can each check return BLOCKED, and does a missing input produce it?
- Does every FAIL line name the item?
- Is there a test for each verdict?
- Would a stranger run it without asking you a question?
