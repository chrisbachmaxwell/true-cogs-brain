# Goal 03 — Automated reconciliation checks ("Checks" page)

**STATUS: DONE — shipped and verified (see log/2026-07-17-cleanup-run-and-2024-taxes.md and project-status).** Kept for the reasoning record.

**Status:** ACTIVE — approved by Chris 2026-07-15 ("build all of them").

## Why
Every methodology bug found during the build was caught by a hand-run tie-out: the $569k credit-memo discovery came from "income must not exceed bank inflows"; the double-tax bug from "tax deducted exactly once"; the H1 statement was trusted only after money in − money out matched the actual bank change. Encoding those invariants as automatic monthly checks catches that class of problem the day it appears.

## Design
A "Checks" report (fifth tile on the hub): for the selected range, run every invariant and show a green/red board, each check clickable through to the goal-02 drill-downs for the numbers involved.

Checks to encode (all previously validated by hand for H1 2026 — see concepts/h1-2026-verified-numbers.md):
1. **Income ≤ total bank inflows** (violation means non-cash counted as income — the $569k class).
2. **Income decomposition exact:** deposit income lines + bank-landed payments − refunds = statement income, to the cent.
3. **Tax remitted = sales-tax ledger movement** for the range.
4. **Direct method ties:** money in − categorized out − API-invisible remainder = actual bank change from the balance sheet; remainder flagged if it drifts far from payroll-scale norms.
5. **Cash COGS consistency:** statement inventory purchases = inventory-spend engine total for the same range/accounts.
6. **Drill-down sums = statement lines** (goal 02's check lines, run server-side across all lines).
7. **Count freshness:** warn when the latest month-end has no inventory count (Chris enters these monthly — D11).
8. **Offsets sanity:** rebates + reimbursements are negative-COGS/expense reductions, never additive.

## Done when
- [ ] /checks page runs all checks for any range and renders green/red with the measured values and differences.
- [ ] For H1 2026, checks 1–6 and 8 are green; check 7 correctly reports the most recent missing count.
- [ ] Each red check links to the drill-down(s) needed to investigate it.
- [ ] Checks run from cached/mirror data (no QBO hammering) and finish in seconds for cached ranges.
- [ ] `npm run build && npm test` passes with unit tests per check on fixture data.

## Waiting on Chris
Just the go-ahead ("yes, build goal 03") — and later, monthly: enter the month-end inventory count so check 7 stays green.

## Stop clause
If a check is red for a CLOSED, previously-verified month (H1 2026), do not "fix" the check to make it green — investigate whether code regressed or the brain's reference numbers are wrong, correct the right one, and note it in log/.

## Iteration log
- 2026-07-15: proposed and specced; awaiting Chris's approval.
- 2026-07-15: Chris approved ("build all of them").
- 2026-07-15 (worker): implemented and deployed — `/api/checks` + /checks page (fifth hub tile): income-vs-inflows, income decomposition, tax-vs-ledger, direct-method tie (15% payroll-share threshold), COGS/NOI arithmetic, drill-down sums, count freshness, offset sanity. Note: checks 7 (count freshness) correctly warns until Chris enters the July count. Awaiting tester pass + architect ruling.
