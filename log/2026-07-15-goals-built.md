# 2026-07-15 (evening) — Goals 01–03 built and deployed

Chris: "build all of them please." Same session as brain creation; all three goals implemented in one push (`git log` in true-cogs shows the combined commit after `71f9201`).

## Shipped
1. **Goal 01 — password auth.** Users table, scrypt hashing, login/password/users pages, admin user CRUD, forced first-login password change, per-request user recheck (removed user = dead session), rate-limited login. Magic-link code deleted. Gate stays INACTIVE until Chris sets `ADMIN_EMAIL` + `ADMIN_INITIAL_PASSWORD` in Railway and redeploys (commands in goal 01).
2. **Goal 02 — P&L drill-down.** Every statement amount clickable; `/api/pnl-detail` + `/api/expense-detail`; sum-check line on every list; QBO deep links; CSV export; expenses drill total → account → transactions with the payroll/API-invisible remainder shown as an explicit labeled row.
3. **Goal 03 — /checks.** Eight automated tie-outs with a green/red scoreboard, each linking to the report to investigate. Fifth tile on the hub.

## Test/build state
`npm run build` clean; **33 unit tests** pass (was 28; auth tests rewritten for passwords, new pnlDetail tests assert detail sums equal the P&L engine's numbers and expense sign conventions).

## Verification
Live verification results recorded in the goal files' iteration logs. H1 2026 reference NOI $713,506.82 unchanged (see concepts/h1-2026-verified-numbers.md).

## Still on Chris
- Set the admin password (goal 01 "Waiting on Chris" — two paste-ready commands).
- Enter the 2026-07-31 inventory count when July closes (checks page will warn until then).
- Rotate the Railway token (roadmap #4).
