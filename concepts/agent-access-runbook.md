# Agent access runbook — how a new session gets full capability, safely

**As of 2026-07-19.** Goal: a fresh agent can do everything the 07-17/19 sessions did —
deploy, run the admin-gated cleanup engine, re-auth QuickBooks. The rule that makes this
safe: **the brain holds the PROCEDURE; secret VALUES never get committed here.** This repo
is on GitHub and its history is permanent — a password or token committed once is exposed
forever. Keep the two apart.

## The three things an agent needs, and where each secret lives

| Capability | Secret it needs | Where the secret lives (never here) |
|---|---|---|
| Deploy (`railway up`) | `RAILWAY_API_TOKEN` | Railway → Account → Tokens; provide per session (see below) |
| Sign in to run the cleanup/reclassify engine | admin email + password | Chris's password manager |
| QuickBooks read/write | `TOKEN_ENCRYPTION_KEY` + stored OAuth tokens | Railway service env only — never materialize locally |

## Preferred: set them as environment secrets (paste nothing each session)
The cleanest option — a new agent simply *has* these, and nothing lands in chat or git:
- Set `RAILWAY_API_TOKEN` (and, if you want the agent to sign in non-interactively,
  `AGENT_EMAIL` / `AGENT_PASSWORD`) as **secrets on the Claude Code environment** that runs
  these sessions, the same way `QBO_CLIENT_SECRET` is a Railway env var. The agent reads them
  from the environment; they never appear in a message or a commit.
- This is the "do everything I can" switch. Once set, the opener in agent-onboarding.md is
  enough.

## Fallback: paste per session (ephemeral, acceptable)
If you'd rather not store them on the environment, paste the secret into the FIRST message of
the session and tell the agent to use it and not repeat it back. It lives only in that
session's transcript, not in the brain. Use this for the Railway token and, when the engine
must run, the admin password. Rotate the Railway token periodically regardless (it has been
exposed in past transcripts — security-notes.md item 2).

## What the agent does with each

### Deploy
```bash
RAILWAY_API_TOKEN=<token> railway up --detach
```
Then poll `railway deployment list --json` to SUCCESS, wait ~45s for the old container to
drain, and probe a NEW route/string before trusting results (deploy-race-stale-recompute.md).

### Sign in to run the cleanup engine (admin-only routes)
The engine (`/api/reclassify/*`, `/cleanup`) requires an ADMIN session cookie.
- The service account `claude-agent@pictureline.com` is admin **only while**
  `AGENT_IS_ADMIN=true` on Railway (D28; bootstrapAdmin syncs the flag down every boot).
- Log in by POSTing email+password to `/api/login` and saving the cookie, then call the
  reclassify endpoints with that cookie. The account can be reset or removed anytime from
  `/users`.
- **When cleanup work is done, set `AGENT_IS_ADMIN=false` and redeploy** to drop the agent
  back to read-only. Leaving it admin is a standing risk, not a convenience.

### Re-auth QuickBooks (only if disconnected)
Visit `/connect` as an admin of the QBO company; `/callback` stores fresh tokens. Tokens
auto-refresh ~5 min before expiry. The agent cannot do this alone — it needs Chris's Intuit
login — and that's by design.

## The one thing that must NEVER be automated without a fresh explicit OK
QuickBooks WRITES beyond the approved cleanup engine — especially DELETING transactions
(e.g. the bounced-autopay CCP pair). Each new write capability needs Chris's per-session
approval; the permission classifier enforces this and should not be worked around.
