# Example: LinkedIn sign-in for agents

A worked example of the `agent-signin` skill. The generic steps are the skill; this page is one site's details.

## Setup (once, by a human)

- Create a dedicated agent Google account (not the human's). Sign into it once inside the agent's own browser profile. Never the human's daily browser, never the human's password in a file.

## Sign in (the agent's run)

1. Open LinkedIn in the agent's own headless browser profile.
2. Choose "Continue with Google" and pick the agent account. The session belongs to the agent profile from here on.
3. Verify: the feed or jobs page loads signed-in as the agent account. If a captcha, phone check, or fresh Google prompt appears, stop and report BLOCKED — do not open a visible window.

## Where Easy Apply still needs a human

- Pay or screening answers the agent cannot verify: skip and note, do not guess.
- Anything behind a captcha, phone OTP, or payment: stop, report, wait.
- Read-only job pages need no login at all — do those signed-out.

## Why this stays an example

Every site has its own button and quirks; the ladder (SSO first, vault last, human gates stop cleanly) is what transfers. Write one page like this per site; keep the skill itself site-free.
