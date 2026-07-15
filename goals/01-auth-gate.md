# Goal 01 — Sign-in gate live (username + password)

**Status:** ACTIVE
**Why first:** the dashboard shows Pictureline's full financials and is currently open to anyone with the URL.

**Direction change (2026-07-15, Chris):** password-based user management REPLACES the email magic-link approach (decision D24). No Resend/email dependency — the old blocker is gone. The magic-link code in `src/auth.ts` is superseded; keep the HMAC session-cookie machinery (it's sound), replace the login flow.

## Design constraints
- Passwords hashed with `crypto.scrypt` (Node built-in — no new dependencies), per-user random salt, timing-safe verify.
- `users` table: email, password hash+salt, is_admin, created_at. Sessions stay HMAC-signed 30-day cookies with a per-request user recheck.
- Bootstrap without email: if the users table is empty, `ADMIN_EMAIL` + `ADMIN_INITIAL_PASSWORD` env vars seed the first admin at boot (and the app forces a password change on first login).
- Admin-only user management page: add user, reset a user's password, remove user, and self-serve "change my password".
- Rate-limit login attempts (same discipline as the old flow); generic error on bad credentials (no enumeration).
- Auth enforced whenever the users table is non-empty (replaces the `RESEND_API_KEY` switch).
- `/connect`, `/callback`, `/health`, `/theme.css`, `/app.js`, `/login` stay open — QBO re-auth must never be locked out. No `express.static` (see concepts/security-notes.md).

## Done when
- [ ] With a seeded admin, `curl -s -o /dev/null -w "%{http_code}" "https://web-production-8c8c0.up.railway.app/api/pnl-statement?start=2026-01-01&end=2026-06-30"` returns **401** with no session cookie.
- [ ] Loading `/` in a fresh browser redirects to `/login`, which asks for email + password.
- [ ] Signing in as the admin works and forces a password change on first login; reports then load normally (spot-check /pnl H1 2026 → NOI $713,506.82).
- [ ] Wrong password shows a generic error, and repeated failures are rate-limited.
- [ ] Admin can create a second user from the UI; that user can sign in; admin can reset their password and remove them.
- [ ] A removed user's existing session cookie stops working on the next request.
- [ ] `/connect`, `/callback`, `/health` work without sign-in.
- [ ] `npm run build && npm test` passes, including new unit tests for hashing/verification and the empty-table bootstrap.

## Waiting on Chris
1. Choose your admin password. Then, in a session with the code repo:
   ```bash
   railway variables --set ADMIN_EMAIL=chrism@pictureline.com --set ADMIN_INITIAL_PASSWORD='<the password you chose>'
   railway up --detach
   ```
   Expected: after the 3–5 min deploy, the dashboard URL shows a sign-in page; your email + password gets you in and immediately asks you to set a new password.
2. That's it — no Resend account needed anymore.

## Stop clause
If after deploying the gate the QBO `/connect` flow or the reports become unreachable even when signed in, STOP, roll back by redeploying the previous commit, and report to Chris — never leave the app in a state where he can't see his reports.

## Iteration log
- 2026-07-15 (build session): magic-link auth code complete/tested but never activated (`RESEND_API_KEY` unset).
- 2026-07-15: Chris redirected the goal to password-based user management; goal rewritten, magic-link approach retired (D24).
- 2026-07-15 (worker): implemented and deployed — users table, scrypt hashing, login/password/users pages, admin CRUD, forced first-login password change, per-request user recheck, rate limiting; magic-link code removed. Unit tests cover hashing, tampered/expired sessions, malformed hashes. Gate verified INACTIVE live (`/auth/me` → enabled:false) until Chris sets `ADMIN_EMAIL`/`ADMIN_INITIAL_PASSWORD` — see "Waiting on Chris". Tester/architect should verify the Done-when list once the vars are set.
