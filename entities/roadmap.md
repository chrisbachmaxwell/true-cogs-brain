# Roadmap — WHAT IS LEFT

**As of: 2026-07-17, after the marathon correctness session.** Ordered by what unblocks Chris fastest. Items marked **(Chris)** need his action — always give him paste-ready steps.

## 1. ACH final mile **(Chris)**
14 unmatched payments ($258,668.51) on raw/2026-07-17-ach-final-mile.html, then the closing journal entry (debit 68000 Vendor Overpayment Write-offs / credit ACH for the balance, ≈ +$99,533 once the 14 resolve) → ACH reads $0.00. Then remove/disable the reclassify tool (D27) and revoke agent admin (D28).

## 2. The $500,000 savings question **(Chris — 30 seconds)**
2025-09-15 "ONLINE XFER TO SAV ****3795" is coded to Shareholder Distributions. Zions business savings is ****3795 with book balance $348,759. Check the real savings balance: if it holds ~$500k more, the transfer was internal and is miscoded — 2025 "owners took" drops ~$500k, bank rises the same, and the books need the fix. Log Addendum 7 has the full story.

## 3. Amex/card register repair — DIAGNOSED, one JE fixes it **(Chris's todo — parked by his choice 2026-07-17)**
Twin-matcher falsified the double-payment theory (Addendum 16): the −$2.0M PLATINUM Amex register is **missing charges** (pre-Jul-2022 history never booked $517k; 2024 feed collapse ~$500k; 9 sick months $761k), payments are real. TODO LIST (in order):
- **(Chris)** Verify 3 possible duplicate payments (~$155k) against Zions statements — raw/2026-07-17-amex-repair-checklist.html (Aug-2022 $57,976.42 pair · Dec-2022 $20,000 pair · Jul-2026 bounced autopay $76,640.30 booked twice, return never booked).
- **(Chris + accountant)** ONE truing journal entry: set Amex register = real feed balance (~−$70,706 owed, exact number from the feed that day), offset Retained Earnings/OBE. Prior-period cleanup — accountant's sign-off required. Fixes Posted = Bank.
- Then same pattern: AX Purple (payments miscoded to expense accounts; fixing likely RAISES NOI), "Credit Cards" parent 99 (36 negative lines, −$554,747 since 2023), Chase (small), QB Checking (fix already prescribed).
App reports unaffected throughout (they never read card registers).

## 4. Security chores **(Chris)**
- Revoke agent admin when cleanup ends: Railway → Variables → `AGENT_IS_ADMIN` → `false` → Deploy (bootstrapAdmin syncs the flag down, D28).
- Rotate the Railway account token (`f406ac63-…` appeared in chat): railway.com → Account Settings → Tokens.

## 5. Monthly close routine (live, keep the habit)
Chris enters a physical count each month-end on /pnl (last: 2026-07-15). The statement's count-gap warnings nag when a range's counts are stale.

## 6. Remaining 2023 manual cleanups **(Chris, from the 66000 checklist)**
Direct-deposit pulls (delete 22 + one $48,990.88 transfer from QuickBooks Checking), IRS pair, JENS $67k, teller $24.5k, SUI rows, 401k fees. Each raises 2023 NOI.

## 7. Bookkeeper hygiene list (after Amex)
concepts/ledger-hygiene-items.md: Cash on Hand negative, "Fraud" $84k, frozen A/P 20200 & A/R 11000, AX Purple account typing, sales-tax catch-up JEs (Chris's Option 1), 2022 items (Rent & Lease unnamed checks, Casual Labor capex, Wages-deleted), expense-audit eyeball lists (13/34/6 possible duplicates in 2024/2025/2026).

## 8. Candidate builds (pick when wanted)
- Year-vs-year mirror mode on /money (same categories, two columns) — natural v3 after the waterfall.
- Sankey "money river" view — prettiest, held for later.
- Bank-ledger CSV reconciliation (parked by Chris) — would itemize the payroll-invisible block.
- Boise credit-memo audit (parked; the conduit principle in vendor-rebate-netting-question.md explains the mechanism, the per-memo audit remains).

## Done since the last roadmap (2026-07-15 → 17)
Goals 01–04 shipped and verified (password auth live · P&L drill-downs → now inline drawers · /checks 9/9 on the corrected engine · dashboard consistency). ACH cleanup: $13.06M → −$159k via the admin-gated reclassify engine (D27/D28). Tax pulls 2023 (verified hand-done) + 2024 ($298k, engine). Feed-refunds income fix (D-feed). THE credit bug (D33): bill-payment lines are blended cash+credits — ~$1.2–1.5M/yr of phantom COGS removed; all years land on Chris's calibration (2023 $1.54M · 2024 $1.02M · 2025 $1.65M · H1-26 $1.33M). Cost basis = cash paid for the bill, on payment date (D32→D33). Exclusion (D26) removed. Profit-to-bank proof method + /money waterfall report + inline drawers everywhere + year/period picker + P&L compare + /cashflow & /bank date-format fix.

## Explicitly out of scope (agreed)
Multi-company support, per-location P&L splits, QuickBooks writes beyond the one-time admin-gated cleanup engine (D2/D27 — engine to be disabled when ACH hits $0.00).
