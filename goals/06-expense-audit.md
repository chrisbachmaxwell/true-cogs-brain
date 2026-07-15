# Goal 06 — Full expense audit ("I can't trust the accountant")

**Status:** ACTIVE — requested by Chris 2026-07-15: "we are going to need to audit all the expenses… lets check all the expenses to make sure they are correct."
**Why:** three independent expense problems were found in one day, all from the 2023–2025 bookkeeping era (fake ACH bill payments, unnamed feed twins, tax pulls filed as payroll). Assume nothing else is clean until checked.

## Method (proven on account 66000 — replicate per account)
For each year (2022 → 2026 YTD) and each expense account on the books' P&L:
1. Pull the account's transactions (`/api/expense-detail?account=<id>`); note the API-invisible remainder (payroll accounts legitimately have one; others should not).
2. Classify each transaction by its **bank-feed memo** (the memo tells the truth — the category often lies): tax memos in non-tax accounts, PAYROLL memos outside payroll, personal names, TELLER/cashed checks, unnamed payees, round numbers.
3. Cross-account duplicate scan: same amount ±$1 within ±14 days appearing in two accounts.
4. Year-over-year swing table per account (>50% swings flagged, like Chris caught 66000).
5. Output: one **HTML checklist per finding group** (the format Chris loves — checkbox, date, amount, deep link, memo, exact fix), archived in brain `raw/`.

## Already-known targets (do these accounts first)
- `Wages (deleted)` — **$4.67M in 2022** expense on a deleted account; 2022 opex total ($11.9M!) is wildly out of line with 2023–25 (~$3M). 2022's whole expense section needs the sweep first.
- 66000 Payroll Expenses — DONE (checklists delivered for 2023–2026).
- 65300 Payroll — verify the invisible remainder matches actual payroll runs (Chris knows headcount).
- Any account with TAX memos outside 21900/W-H accounts.

## Done when
- [ ] Every expense account ≥ $10k/year for 2022–2026 has been swept with the memo classifier.
- [ ] Cross-account duplicate scan run for each year; findings checklisted.
- [ ] Chris has an HTML checklist per finding group, each item with a QBO link and a specific fix.
- [ ] A summary page ranks findings by dollar impact on each year's NOI.
- [ ] After Chris applies fixes: affected year statements refreshed and the revised NOIs recorded in the brain.

## Waiting on Chris
Nothing to start. As fixes land, refresh affected ranges (`Refresh` button) — closed-year caches recompute on demand.

## Stop clause
Findings are RECOMMENDATIONS with evidence — never state a transaction is wrong without showing the memo/duplicate evidence, and anything involving owner/personal items goes to Chris + accountant, not unilateral judgment.

## Iteration log
- 2026-07-15: goal created. 66000 checklists (2023–26, $1.06M total misfiled) and ACH checklist (485 payments with bills/credits/twins) delivered.
- 2026-07-15 (later): 2022 sweep DONE — findings: "Rent & Lease" $4.02M = 50 unnamed vendor checks (pre-ACH unmatched-payments era, belongs in COGS); "61200 Casual Labor" $1.67M mostly store build-out (Zwick Construction + architects + wires → Leasehold Improvements CAPEX — raises 2022 NOI dollar-for-dollar); "Wages (deleted)" $4.67M mixed (1.6M flagged memos + 1.54M invisible paychecks); "Taxes (deleted)" $654k remittances. True 2022 opex ≈ $4M, not $11.9M. Report: raw/2026-07-15-expense-audit-2022.html.
- 2026-07-15 (later): 2023–2026 sweeps run (38/38/37/33 accounts) — per-year audit reports generated the same way.
