---
name: in-your-own-words
description: "Before building, restate the goal and the problem in your own words and compare them with the request; stop and flag any mismatch instead of building the wrong thing. Use at the start of any non-trivial task."
---

# In Your Own Words

Most wasted work is a correct build of the wrong thing. A short restatement catches that
before any code or form is touched.

## Steps

1. Read the request and whatever it links to. Do not restate from the title alone.
2. Write, without copying any sentence from the request:
   - **Goal:** who benefits and what is true when this is done (one line).
   - **Problem:** what is wrong or missing today (one line).
   - **Done when:** the check that will prove it (one line; see the verify-pattern skill).
3. Compare with the request. If any line contradicts it, or you could not write one
   without guessing, stop. Report the mismatch or the missing fact as one question — do
   not build around it.
4. Put the restatement at the top of your receipt, as `Restated:`.

Skip only for trivial tasks: one file, one obvious change, nothing to misread.

## Example

Request: "Make the nightly report less noisy."

```
Restated: Goal — the reader sees only items that need action. Problem — every run lists
all 40 checks, passing ones included. Done when — a run with no failures prints one line.
```

If the requester actually wanted fewer reports per week, step 3 catches it before work
starts.
