# SETUP — stand this layer up on any agent environment in about a day

One sentence: copy the skills and rules shapes into the harness you are allowed
to use, run three checks, and you have a working agent setup with the same
disciplines our daily fleet runs on.

Time budget: ~1 day including adaptation. Steps 1–3 are under an hour; the rest
is adapting the rules ledger to your own team and testing.

## 0. Prerequisites

- Git, and any AI coding agent that reads one of: `AGENTS.md`,
  `.cursor/rules/`, `.claude/` (with a `skills/` dir), `.clinerules/`,
  `opencode` config, or the agentskills.io skill format.
- Node.js (only if you want rulesync to generate per-harness files; optional).
- No accounts, no services, no spend. Everything here is local files.

## 1. Fetch the layer (5 min)

```bash
git clone https://github.com/krantikaridev/agent-skills.git
cd agent-skills
```

Or pull just the skills into an existing project with rulesync:

```bash
npx rulesync fetch krantikaridev/agent-skills   # files into .rulesync/
npx rulesync install                             # declarative rules+skills
```

## 2. Load it into your harness (15–30 min)

Pick your row. "Skills" = the `skills/` directories; "Rules" = paste or
generate the text from `agent-patterns/rules-ledger.md` into the rules file.

| Harness | Skills load from | Rules load from |
| --- | --- | --- |
| Cursor | `.cursor/rules/` or project `skills/` (recent versions) | `.cursor/rules/*.mdc` or `AGENTS.md` |
| Claude Code | `.claude/skills/` | `CLAUDE.md` or `AGENTS.md` |
| Cline | workspace rules + skill folders | `.clinerules/*.md` |
| opencode | `skills/` via config | `opencode.jsonc` instructions / `AGENTS.md` |
| Any agent reading AGENTS.md | link or copy `skills/`, name the folder in `AGENTS.md` | `AGENTS.md` itself |
| rulesync-managed repo | `.rulesync/skills/` symlinks | `.rulesync/rules/*.md`, `npx rulesync generate` |

Manual fallback that works everywhere: keep this folder next to the project and
tell the agent, in its rules file, "load skills from `<path>/skills/`; before a
non-trivial task, check whether one matches."

## 3. Test it (30 min) — the three checks

1. **Skill load:** ask the agent to use the `verify-pattern` skill to write a
   check that proves this repo was cloned correctly (expected: PASS/FAIL/BLOCKED
   shape, exit codes 0/1/2, an evidence line). It must name the skill it loaded.
2. **Rule reach:** put one rule line from `agent-patterns/rules-ledger.md` into
   your harness's rules file (e.g. the receipt rule), ask the agent to do any
   small task, and confirm the output ends with the receipt fields.
3. **Generic guard:** run any skill against your own account/site placeholders —
   nothing in `skills/` should reference our machine, so if a step says "ask a
   human for X", X must be a `<placeholder>`, not a name.

If all three pass, the layer is up. Anything else is preference tuning.

## 4. Adapt the rules ledger to your team (2–4 h, the real work)

- Copy `agent-patterns/rules-ledger.md` and start empty: rules you add come from
  real mistakes, one line each — `id (date, why): rule. Check: how to verify.`
- Wire the ledger to the harness files with one generate step (rulesync, or a
  20-line script) so there is exactly one place to edit.
- Add the two mechanical guards that make rules stick: every task prompt starts
  with the rendered rules, and every run ends with a receipt a linter can read.
  See `agent-patterns/receipts-and-preflight.md`.

## 5. Daily operation

- Run `agent-patterns/open-loops.md`'s ledger once a day or per PR: promises
  have an owner and a date; closing needs evidence.
- When a task has been done three times, write its canonical script and make the
  agent use it (the preflight pattern).
- Re-sync with upstream occasionally: `git pull` here, `--check` on the source
  repo's exporter keeps the public layer honest; you will see drift as a diff.

## Troubleshooting

- **Agent ignores skills:** the rules file must explicitly say to query skills
  before acting (see `rules-ledger.md`'s discovery rule) — discovery is a rule,
  not a hope.
- **Harness has no skills support:** use the manual fallback (§2) and one rule
  line pointing at the folder; agentskills.io format is plain Markdown.
- **Something looks personal or fleet-specific:** open an issue; that is a bug
  in our export, not a feature.
