# Goal 01 — Sign-in gate live

**Status:** BLOCKED (waiting on Chris) — all code exists (`src/auth.ts`, decision D17); it activates when `RESEND_API_KEY` is set.
**Why first:** the dashboard shows Pictureline's full financials and is currently open to anyone with the URL.

## Done when
- [ ] `curl -s -o /dev/null -w "%{http_code}" "https://web-production-8c8c0.up.railway.app/api/pnl-statement?start=2026-01-01&end=2026-06-30"` returns **401** (not 200) with no session cookie.
- [ ] Loading `https://web-production-8c8c0.up.railway.app/` in a fresh browser redirects to `/login`.
- [ ] Requesting a link on `/login` for `chrism@pictureline.com` delivers an email whose link signs in successfully (30-day session).
- [ ] Requesting a link for an email NOT on the allowlist shows the same neutral "check your inbox" message (no enumeration) and no session is created.
- [ ] A used magic link, reused, does NOT sign in (single-use).
- [ ] `/connect`, `/callback`, and `/health` still work without sign-in (QBO re-auth must never be locked out).
- [ ] Reports still load normally after sign-in (spot-check /pnl for H1 2026 → NOI $713,506.82).

## Waiting on Chris
1. Create a free account at **https://resend.com** → API Keys → Create API Key (name it `pictureline-tracker`). Copy the key (starts with `re_`).
2. Verify a sending domain in Resend (Domains → Add Domain → follow the DNS steps), or use Resend's onboarding sender for testing.
3. In a session with the code repo, paste the key into:
   ```bash
   railway variables --set RESEND_API_KEY=re_XXXXXXXX --set AUTH_FROM_EMAIL="reports@<your-verified-domain>"
   railway up --detach
   ```
   Expected: deploy finishes in 3–5 min; afterwards the dashboard URL redirects to a sign-in page.
4. Tell the next agent "the Resend key is set" — it will verify every box above.

## Stop clause
If the login email does not arrive within 5 minutes, or QBO `/connect` becomes unreachable, STOP, set `RESEND_API_KEY` back to unset (`railway variables --set RESEND_API_KEY=`), redeploy, and report to Chris — never leave the app in a state where reports are broken.

## Iteration log
- 2026-07-15 (session that built the project): auth code complete and unit-tested; gate verified inactive (API returns 200 unauthenticated). Blocked solely on the Resend key.
