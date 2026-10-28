---
name: agent-signin
description: "Sign a website into an AI agent's own account without borrowing a human's password or browser, walking an SSO-first ladder and stopping cleanly at human-only gates. Use when an agent must do real work behind a login."
---

# Agent Sign-In

Teach an AI agent to sign into a website on its own — without borrowing a human's password or browser — so it can do real work behind a login.

## Who this is for

Agents *without* built-in identity: a coding agent on your laptop, a CI runner, a small fleet of helpers. If your agent platform already gives each agent its own email and login, you do not need this.

## The pattern in 30 seconds

1. **Give the agent its own account** on the identity provider (e.g. its own Google account), separate from any human's. The agent never sees, stores, or types a human password.
2. **Sign in once, in a separate browser profile** that belongs to that agent account. Never drive the human's daily browser.
3. **Ride the single-sign-on into the target site** ("Continue with Google" → the site). Only when a site offers no SSO path at all, fall back to a scoped vault password as the last rung.
4. **Stop cleanly at human-only gates.** Captcha, phone OTP, payment, or a fresh SSO prompt the agent cannot answer = stop, report BLOCKED, never open a visible window or ask for a secret.

## The ladder (try in this order, stop at the first that works)

1. **Agent SSO first** — sign the agent account into the target site via the provider button. This covers most sites that offer "Continue with Google / GitHub / LinkedIn".
2. **Scoped vault password** — only when the site has no SSO path: read one login from a scoped vault folder, exact-name-only, one field to stdout, no writes. Never a shared or human vault root.
3. **Emailed login code** — only when the site sends a code to the agent account's own inbox: read the preview line (sender, subject, snippet) from allowlisted senders, never open / click / mark-read, fail closed when the session is gone (run the session probe below first).
4. **Human gate** — captcha, phone OTP, payment, or a sign-in prompt only a human can answer: stop and report BLOCKED with what was tried and what is needed. Never type credentials, never open a visible window.

## Rules that keep it safe

- **One browser per agent.** Each agent gets its own headless browser profile on its own port. A visible window is a bug. Never attach to the human's browser.
- **Never the human's session.** No reuse of the human's cookies, SSO, or signed-in tabs — even when it would be faster.
- **Fail closed.** No session = refuse with a clear message, never a silent fallback to asking the human for a password.
- **Human-only gates stay human.** The agent reports what it needs; the human acts in their own browser. Nothing secret is typed, pasted, or stored by the agent.
- **Least privilege.** The agent account gets the smallest access that finishes the job. Money, send, delete, and permission scopes are never in the agent's reach.

## Try it

Pick one site your agent needs. Walk the ladder: agent account → separate profile → SSO into the site → stop cleanly if a human gate appears. If step 1 works, you are done — steps 2–4 exist only for sites that need them.

## Example

`examples/linkedin-signin.md` shows this pattern applied to LinkedIn: which button to press, which profile to use, and where Easy Apply still needs a human. The generic steps above are the skill; the LinkedIn page is one worked example, not the headline.

## Config

The account, browser profile, mailbox and vault names this fleet uses are not in this
file: they come from `$DATA_REPO/skills/config.md` (`<mail-account>`,
`<browser-profile>`, `<human-browser>`, `<vault>`). The procedure above is generic and
can be published as-is; the config is personal and is not.

## Two checks before any emailed step

Both end in one line, `OK ...` or `BLOCKED <reason>: <next step>`, so a stop is clean and
the next agent knows exactly what is needed.

1. **Session-health probe before each read.** A mailbox or portal session can sign out
   silently. Before every read, run the session's own read-only check and stop on a fail:
   `python3 scripts/signin_gate.py probe --name mailbox -- <check command>`.
   Exit 0 = read; exit 2 = `BLOCKED mailbox-signed-out` (or `-probe-missing`,
   `-probe-timeout`): do not read, do not retry, report the line. A human re-signs the
   session once; nobody types a password for the agent.
2. **Request and watch inside the link lifetime.** A reset link expires (often 1-2 hours).
   Record the request time, read only mail received after it, and gate the link before use:
   `python3 scripts/signin_gate.py window --requested-at <ISO> --lifetime-min <n>
   [--received-at <ISO>] [--margin-min 10]`. `BLOCKED reset-link-expired`,
   `-too-late` or `-stale` means request a fresh link and read it at once; never reuse an
   older link. If the reader is a different agent, the handoff carries the request time.
   When you request the link yourself, do not wait by hand: the same agent runs
   `python3 scripts/signin_gate.py wait --site <site> --requested-at <ISO> --lifetime-min <n>`
   right after the click — it refuses up front when the mailbox session is dead, polls
   until the mail lands, and writes the link to a local file outside any repo (#1316:
   six links died unread because nothing consumed them inside their lifetime).

Anything the probe or window prints is one line with code-like digit runs redacted; codes
and links are never written to a log, a ticket or a repo.

## What this does not cover

- **Delegated reads across accounts.** Reading another account's saved items needs that account's explicit delegation (an invite accepted by the owner). The ladder assumes the agent account owns what it reads. Gap: a delegation-check step.
- **No-SSO sites at volume.** Sites with no SSO path need one vault entry each, created by a human. At volume this becomes a queue of human writes. Gap: a bulk-request format for the human.

- **Expiry race on reset links.** A reset link expires (often 1-2 hours) and can die while queued for relay. Gap: a request-and-watch loop - request one link, read only mail received after the request time, gate the link inside its lifetime, expire loudly if missed.
- **Silent session decay.** A mailbox or profile can sign out with no early signal; the next read fails cold. Gap: a session-health probe before every read.

Each gap is a candidate check in the next version; until it is written down, an agent
that hits it stops and reports it rather than improvising.
