# Goal 02 — P&L drill-down: click any number to see what's inside it

**STATUS: DONE — shipped and verified (see log/2026-07-17-cleanup-run-and-2024-taxes.md and project-status).** Kept for the reasoning record.

**Status:** ACTIVE (starts after goal 01)
**Why:** Chris (2026-07-15): "I would like to be able to click into a total amount and then it would show me all the transactions or accounts that make up the number… This drill down is important to me so I can see where our numbers are coming from." He audits by checking numbers against his own knowledge — the drill-down is how he checks the app.

## Design
Every amount on the P&L statement becomes clickable and opens a detail view (same page, sliding panel or section — keep the Apple-like feel) showing the rows that make up the number. Per line:

| Statement line | Drill-down shows |
|---|---|
| Money in — deposits | Deposit income lines: date, source account, memo, amount |
| Invoice payments (bank-landed) | Payments: date, customer, deposit account, amount |
| Customer refunds | Refund receipts: date, customer, amount |
| Sales tax remitted | The traced remittance transactions against 21900 |
| Inventory purchases | REUSE the existing transaction audit (`/api/inventory-spend` — already built, month-keyed; extend to ranges) with vendor, source doc, allocation ratio |
| Inventory change | The two counts used (dates + values) with a link to the counts card |
| Freight/repairs/materials | Cash-traced transactions per account (51300/50600/50200 sub-grouped) |
| Vendor rebates / reimbursements | The deposit lines classified as offsets |
| Operating expenses | Account-level breakdown (exists as a table today) → each account clickable → its transactions from the mirror |
| NOI / totals | The arithmetic: each component line with its value, restated |

## The three enhancements (agreed with Chris 2026-07-15)
1. **Self-verifying drill-downs:** every detail view ends with a check line — "N transactions sum to $X — matches the statement ✓" (red + difference when it doesn't). A drill-down that doesn't tie out is a bug surfaced, not hidden.
2. **QBO deep links:** each transaction row links to the source document in QuickBooks (`https://qbo.intuit.com/app/<doctype>?txnId=<id>`), so Chris can inspect the original bill/deposit/payment.
3. **CSV export** button on every detail table (for the bookkeeper).

## Engineering notes
- Serve details from the Postgres mirror (`qbo_txns`) — fast and rate-limit-free; fall back to remote only when the mirror is stale (see concepts/qbo-rate-limits-and-sync-mirror.md).
- Books-expense transaction detail is the one part the mirror may not fully cover (accrual P&L rows come from the report API; payroll is API-invisible — concepts/qbo-api-invisible-transactions.md). Where transactions can't be enumerated, show the account breakdown and label the remainder honestly (e.g. "payroll — visible in QuickBooks only, $X"). Never present a partial list as complete.
- New endpoints must use the shared cache/dedupe discipline and `staleGuard()` on the frontend (concepts/async-stale-response-guard.md).

## Done when
- [ ] Every amount on /pnl is clickable and opens a detail view with the rows behind it.
- [ ] Each detail view shows the sum-check line, and for H1 2026 every drill-down sum matches its statement line exactly (reference: concepts/h1-2026-verified-numbers.md).
- [ ] Expenses drill down two levels: total → accounts → transactions per account, with any non-enumerable remainder (payroll) labeled as such.
- [ ] Each transaction row deep-links to the document in QuickBooks (spot-check 3 different doc types open correctly).
- [ ] Every detail table has a working CSV export.
- [ ] Range changes while a panel is open can't paint stale data (staleGuard applies to detail fetches).
- [ ] `npm run build && npm test` passes with unit tests for the new detail endpoints' sums.

## Waiting on Chris
Nothing to start. When it ships: click through a month you know well and tell us any number whose composition surprises you — that's the acceptance test.

## Stop clause
If a drill-down sum does NOT match its statement line for a closed month, stop adding features and treat the mismatch as the bug to fix (or the brain page to correct) before shipping.

## Iteration log
- 2026-07-15: goal created from Chris's request; enhancements 1–3 proposed and accepted.
- 2026-07-15 (worker): implemented and deployed — `/api/pnl-detail` (deposits, invoicePayments, salesReceipts, refunds, salesTax, inventory, directCosts, rebates, reimbursements, inventoryChange) and `/api/expense-detail` (per-account rows from the mirror with the API-invisible remainder shown explicitly); every statement amount on /pnl clickable with sum-check line, QBO deep links, CSV export; expense table drills into accounts. `SpendTransaction` now carries `txnId`. Unit tests: detail sums equal computeMonthlyPnl fields; expense sign conventions.
- 2026-07-15 (Chris): requested MULTI-LEVEL drill-downs ("when I click expenses it would be broken down by expense categories and if I click the total again than it would have all the transactions in that category. do this for all that make sense").
- 2026-07-15 (worker): shipped two-level everywhere it makes sense — level 1 categories, level 2 transactions, sum checks at BOTH levels, back button, category-aware CSV. Groupings: operating expenses → books accounts → transactions; inventory & sales tax → payee; direct costs → cost account (#51300/#50600/#50200, computed per-account server-side); deposits → income account; payments/receipts/refunds → customer; rebates/reimbursements → receiving account. Flat lists auto-skip the category level when there's only one group. Books expense list no longer truncated to 30 so the category level ties to the books total. Awaiting tester pass + architect ruling.
