# agent-skills — portable skills and agent patterns

A public, agent-agnostic layer of AI-agent **skills** (agentskills.io format)
and **agent patterns** (how one set of rules reaches every harness), extracted
from production daily work: sign-in ladders, verification gates, storage
hygiene, and the discipline of writing rules once and generating them for
Cursor, Claude Code, Cline, opencode, or any agent that reads `AGENTS.md`.

- **First published:** 2026-10-28 (public on this date; content committed with
  this date in the history).
- **Source:** maintained in a private daily-ops repo and exported here
  deterministically (`export --check` in that repo guards against drift).
  This repo is the public mirror of record; issues and PRs are welcome here.
- **License:** MIT.
- **No personal data:** every file is generic by policy. Export tooling refuses
  files carrying personal names, account handles, private paths or local
  ports. If you find such a leak, open an issue — it is treated as a bug.

## Contents

```
skills/          one directory per skill, SKILL.md with name+description front matter
agent-patterns/  the reusable shapes behind the skills (rules ledger, harness map,
                 receipts, open loops, preflight)
SETUP.md         "stand this up on setup X" — a one-day playbook for any harness
manifest.json    what was exported, from which commit, on which date
```

## Skills shipped (v0.1)

- `agent-signin` — sign an agent into a site with its own account: SSO-first
  ladder, separate headless profile, clean stop at human-only gates.
- `in-your-own-words` — restate the goal before building; stop on mismatch.
- `stay-in-free-tier-storage-manager` — free a full storage tier by moving
  clutter to a drive you own, verifying bytes, never deleting the last copy.
- `verify-open-source` — pass/fail: is a public repo a credible v0.1 to clone?
- `verify-pattern` — the reusable shape for any check: PASS/FAIL/BLOCKED,
  fail-closed inputs, evidence line, one hermetic test per verdict.
- `verify-interview-ready` — pass/fail readiness gate before an interview.
- `verify-showcase` — pass/fail: does the profile show real shipped work?

## Quickstart (5 minutes)

```bash
git clone https://github.com/krantikaridev/agent-skills.git
cd agent-skills
# Point your agent at the folder. Examples:
#   Cursor / Claude Code / Cline / opencode: copy or symlink skills/ into the
#   project's skills dir, and paste agent-patterns/rules-ledger.md into AGENTS.md
#   rulesync users: npx rulesync install  (see SETUP.md for the declarative form)
```

Then run the three checks in `SETUP.md` § "Test it" — if the agent can load a
skill, recite an applied rule, and finish a task with a receipt, the layer is up.

## Why this exists

Teams that run AI agents daily converge on the same handful of disciplines:
sign agents into sites with their own accounts and SSO first; never claim a
task is done without a fresh checkable artifact; keep recurring promises in a
ledger with an owner and a date; write rules once and generate them per
harness instead of maintaining five copies. These are those disciplines,
scrubbed and packaged so any environment can pick them up in about a day.

## Contributing

Skills must stay generic (no person, account, or machine-specific path —
configuration comes from your own local file) and must ship with a way to
verify them. Open an issue first for new-skill proposals.
