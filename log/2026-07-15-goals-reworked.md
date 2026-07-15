# 2026-07-15 (later) — Goals reworked per Chris

Same day as brain creation; Chris reviewed the plan and redirected.

## What changed
1. **Goal 01 rewritten:** password-based user management replaces email magic-link auth (Chris: "instead of doing a login with email, lets use a user management with passwords"). Recorded as D24. The Resend dependency — the goal's only blocker — is gone; the magic-link code is retired unactivated. security-notes, project-status, and roadmap updated to match (rule 3: pages contradicted by the new direction were fixed this session).
2. **Goal 02 created:** P&L drill-down — every statement amount clickable through to its transactions/accounts; expenses two levels deep. Chris's stated purpose: "so I can see where our numbers are coming from… this way I can check it." Enhancements agreed: self-verifying sum-check line on every detail view, QBO deep links per transaction, CSV export per table. Recorded as D25.
3. **Goal 03 proposed (awaiting Chris's go):** a "Checks" page automating the H1 tie-out invariants (income ≤ bank inflows, income decomposition exact to the cent, tax = ledger movement, direct-method ties, drill-down sums = statement lines, count freshness, offset signs). Rationale: every methodology bug found during the build was caught by one of these hand-run checks.
4. Roadmap reordered: auth → drill-down → checks → token rotation → monthly close → parked items.

## Next
Worker starts goal 01 (password auth). Chris owes nothing until it ships (then: choose an admin password; `railway variables --set ADMIN_EMAIL=… ADMIN_INITIAL_PASSWORD=…`). Goal 03 waits for his explicit yes.
