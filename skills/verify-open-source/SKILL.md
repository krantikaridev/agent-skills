---
name: verify-open-source
description: "Pass/fail check that a public repository is a credible v0.1 someone can clone and run. Use before naming a repo on an open-source form, listing it as a project, or announcing it."
---

# Verify Open Source

A pass means a stranger can clone the repo, run the quickstart, and understand what it
does in three lines. Run it on every public repo before naming one anywhere. A repo
that fails is a to-do, not a showcase.

Every check fails closed: a missing file, an unreadable repo or a private repo is a
FAIL with a clear message — never a silent pass.

## What a pass needs

1. A LICENSE file.
2. A README that says what it does in 3 lines, with a quickstart that actually runs.
3. At least 1 test that passes.
4. A tagged release.
5. No secrets or personal data (transcripts, CSVs of jobs, emails, health data).
6. An issues-welcome or good-first-issue item.
7. A link back from the owner's profile README (`<profile-repo>`).

## Config

`<owner>` and `<profile-repo>` come from `$DATA_REPO/skills/config.md`. This file stays
generic so it can be published as-is.

## Check

```bash
python3 scripts/check_oss_repo.py <owner>/<repo>
```

**Pass:** exit 0 and every required item is present.

**Fail:** any required item missing, or the repo cannot be read.

## Overall

Run it on every public repo before naming one on a form or in a post. The closest repo
to a pass is the one that goes on the form; the rest get an issue.
