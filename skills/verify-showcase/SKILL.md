---
name: verify-showcase
description: "Pass/fail check that a person landing on an open-source profile or social profile sees real shipped work within 60 seconds. Use before sending anyone to the profile, before a recruiter conversation, or after adding a repo to the pinned list."
---

# Verify Showcase

A hiring manager or a collaborator spends about a minute on a profile. A pass means that
minute lands: real work, runnable, explained in one line each. A fail means the visit
ends with a shrug.

Every check fails closed: a missing README, or a private repo named as public, is a FAIL.

## What a pass needs

1. The profile README (`<profile-repo>`) pins the best repos and carries a one-line who-I-am.
2. Every pinned repo has a 30-second demo (a GIF or asciinema, linked — not committed if large), a live quickstart, and a "why it matters" line.
3. At least one public write-up per shipped item (a post or a blog doc).
4. Nothing private or personal is exposed: no health data, no job CSVs, no transcripts, no emails.

## Config

`<owner>`, `<profile-repo>`, `<showcase-picks>` and `<niche>` come from
`$DATA_REPO/skills/config.md`. The niche matters because a pinned repo that does not fit
it reads as noise, not breadth.

## Check

```bash
python3 scripts/check_showcase.py
```

**Pass:** exit 0 — the profile pins the repos `<showcase-picks>` names, each has a demo
link, a quickstart and a write-up, and no personal data is named.

**Fail:** any of those missing, or a pinned repo is private.

## Overall

Run before sending anyone to the profile. A fail means the 60-second read does not land:
fix the pinned repo, do not reword the pitch.
