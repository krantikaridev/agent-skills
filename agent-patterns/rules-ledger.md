# Pattern: the rules ledger — write a rule once, reach every agent

The problem: every AI harness you use (Cursor, Claude Code, Cline, opencode,
Claude Code's CLAUDE.md, an AGENTS.md reader) has its own rules file, and copies
drift apart within a week. The fix is one canonical rules file plus generation.

## Shape

- One directory, e.g. `.rulesync/rules/` (or plain `rules/`), holding numbered
  rule lines in Markdown. That is the **only** place a rule is ever edited, and
  edits go through review like code.
- A generator renders that source into each harness's expected file:
  `AGENTS.md`, `CLAUDE.md`, `.cursor/rules/`, `.clinerules/`, `.claude/`,
  `opencode.jsonc`, … (rulesync does this out of the box; any templating script
  works). Generated files are never hand-edited — a CI drift check byte-compares
  them against a fresh generate and fails on mismatch.
- Task runners inject the rendered rules at the **start** of every prompt, so
  context loading is not left to the model's discretion.

## Rule anatomy

One line per rule, born from a real mistake, same turn as the correction:

```
R7 (6 Oct, why-it-exists): Judge every instruction against its goal; if it
would not reach the goal, say so and propose better. Check: one-line "why".
```

Fields: `id (date, why): rule text. Check: how to verify.` A rule without a
machine-checkable "Check:" is a preference, not a rule.

## Precedence

Hard blocks > rules > skills > ask the human. State the precedence line at the
top of the generated files so an agent resolving a conflict does not guess.
Non-negotiables (budget caps, send-stops, deny-lists) live in **code** — guard
scripts and tests — never only in prompt text.

## Discovery rule

Add one rule whose whole job is skill discovery: before any non-trivial act,
query the skill catalog with the user's intent in their words and load every
matching skill; the receipt must list which were loaded (or "none, because").
Skills that are never discovered are documentation, not capability.

## Sizing guard

Keep the rendered rules block inside your prompt budget (a few tens of KB). If
it grows past that, split per-agent rule files the generator includes or
excludes by target, rather than trimming content.
