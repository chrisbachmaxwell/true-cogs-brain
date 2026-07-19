# Security notes — current posture and obligations

**As of 2026-07-19.**

## Open items (highest first)
1. **The dashboard is publicly reachable.** Anyone with the URL sees full financials. Current goal: password-based sign-in — [../goals/01-auth-gate.md](../goals/01-auth-gate.md). (The dormant magic-link gate was retired 2026-07-15 in favor of passwords — D24.)
2. **Railway account token `f406ac63-…` was pasted into a chat session** (2026-07-14) and appears in session transcripts. Chris must rotate it (railway.com → Account → Tokens) once deploy work settles. (verify after: 2026-08)

## Agent service account
`claude-agent@pictureline.com` (non-admin viewer) exists so agents can test the gated app; created at boot via `AGENT_EMAIL`/`AGENT_PASSWORD` env (create-only — an admin reset/removal in /users is never clobbered by a redeploy). Chris requested it 2026-07-15. **UPDATE (D28, 2026-07-17): the account is CURRENTLY ADMIN** via `AGENT_IS_ADMIN=true` on Railway, granted so agents can run the cleanup/reclassify engine. Revoke with `AGENT_IS_ADMIN=false` + redeploy when cleanup work settles — leaving it admin is a standing risk. Chris can reset/remove it anytime from /users. The password lives in Railway env and session scratchpads — never in this repo.

## Standing design (don't regress)
- QBO OAuth tokens: AES-256-GCM in Postgres, key from `TOKEN_ENCRYPTION_KEY` (Railway env only — never materialize locally).
- Auth (per D24, replacing magic links): scrypt-hashed passwords with per-user salt, timing-safe verify; 30-day HMAC session cookies with per-request user recheck; no user enumeration; login rate-limited; first admin seeded from `ADMIN_EMAIL`/`ADMIN_INITIAL_PASSWORD` with forced password change. Keep the old flow's disciplines (hashing at rest, timing-safe compares) — they were sound.
- **No `express.static`** — every page is an explicit route so the auth gate can't be bypassed; only `/theme.css`, `/app.js`, `/login`, `/connect`, `/callback`, `/health` are open. Keep it that way when adding pages.
- `requireAuth` must check `req.originalUrl` (mount-relative `req.path` broke API 401s once).
- Errors sanitized via `qboError` — see [token-leak-error-sanitization.md](token-leak-error-sanitization.md).
- App is read-only toward QuickBooks by design (D2); never add write scopes.
- **NEVER commit secret values to the brain (or any repo).** Passwords, the Railway token, `TOKEN_ENCRYPTION_KEY`, and session cookies stay in Railway env / a password manager / environment secrets. The brain documents the PROCEDURE only — see [agent-access-runbook.md](agent-access-runbook.md). Git history is permanent; a secret committed once is exposed forever.
