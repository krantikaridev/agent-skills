---
name: verify-interview-ready
description: "Pass/fail check before an interview: every skill slice is at target, every story carries numbers and answered probes, every interview-stage role has a prep pack, and no probe is left unanswered. Use before any interview is booked."
---

# Verify Interview Ready

A pass means the candidate can be probed on any slice, any story and any role in the
pipeline and answer with a number. It is run before an interview, not after a bad one.

Every check fails closed: a missing file or a corrupt row is a FAIL with a clear
message. Scores and captures are data, so they live in the data checkout —
`$DATA_REPO` — never in the code repo.

## Config

`<target-roles>`, `<drills-app>`, `<apply-tracker>`, `<interview-dir>` and
`<score-notes>` come from `$DATA_REPO/skills/config.md`.

## Check 1: each slice score against the target

```bash
python3 scripts/check_slice_scores.py
```

Slices: system design, analytic, LLM and agentic, communication, leadership. Target is
9/12 clean on each.

**Pass:** every slice is at or above target and every row parses.

**Fail:** any slice below target, a corrupt row, or the scores file is missing.

## Check 2: each story has numbers plus answered probes

```bash
python3 scripts/check_story_followups.py
```

Each story must have a Numbers section with at least one digit, and every numbered probe
under that story must appear answered in the follow-up file.

**Pass:** every story has numbers and every probe is answered.

**Fail:** a story without numbers, an unanswered probe, or any file missing.

## Check 3: every interview-stage role has a usable prep pack

```bash
python3 scripts/check_interview_prep_packs.py
```

A pack must name the role, at least one weak slice, and at least one matching story.

**Pass:** every interview-stage role has a pack with that content.

**Fail:** a role without a pack, a pack missing role / slice / story, or the tracker is
missing.

## Check 4: the weekly drill names the weakest slice

```bash
python3 scripts/weekly_drill.py
```

**Pass:** prints one slice name from the scores file.

**Fail:** the scores file is missing or unreadable.

## Check 5: no probe is left unanswered

```bash
python3 scripts/probe_generator.py
```

**Pass:** prints `OK no unanswered probes`.

**Fail:** prints a probe, or a required file is missing.

## Overall

All five checks must pass. A fail means an interview is at risk: the drill works the
weakest slice first, and an unanswered probe is a question the interviewer will ask.
