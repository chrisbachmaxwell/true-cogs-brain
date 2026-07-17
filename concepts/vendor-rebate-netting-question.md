# Vendor rebates (50500) vs purchases-received — the open COGS question

**Status: FULLY RESOLVED by D33 (2026-07-17, later the same night) — see decisions.md D33; the mechanism was a data-shape bug, not a netting-rule question.** D32 status note kept for history: Chris set the rule himself — "I only want to count what we paid for the bill" — and his corrected calibration (2023 ≈ $1.8M, not $1M) matched full netting. Engine now books each bill at its cash settlement on the bill's date (computePurchasesSettled). The Canon tie-out is DEMOTED to a verification pass (run it if the recomputed years still miss calibration). Original analysis kept below for the record.

## Proven facts
- Bills are booked GROSS: across 467 ACH-era payments, cash paid ($12,441,081.42) + credits applied ($1,405,690.94) = bill totals ($13,878,234.07) within 0.2%.
- VendorCredit docs are mostly coded to **50500 Refunds and Rebates** (COGS-contra in the online books): −$1,032,845 (2023), −$1,461,361 (2024), −$1,166,219 (2025). Credits APPLIED in payments run $1.24M/$1.52M/$1.24M — same order.
- The D31 purchases-received line subtracts only INVENTORY-CODED credit lines ($280k/$289k/$163k) — so it misses most of 50500.
- Accountant's 2025 P&L (Chris supplied, NOI $1,081,232.95) nets only −$218,730 of refunds/rebates BUT its purchases line ($17,646,981) and inventory values (begin $2,516,665 / end $2,560,218) come from THEIR OWN books, not this QBO file (online 40100 2025 = $21.95M vs their 40100 = $11.12M — different account structures entirely).

## Why not just net all of 50500 into purchases
Netting it naively puts 2023 NOI ≈ $1.8M — overshooting Chris's $1M/yr calibration as badly as the old basis undershot. Suspicions: part of the credits are RETURNS (goods left inventory, so counts already capture them — netting is still formula-correct for those) vs credits already inside bill lines vs pre-period credits. Also a 3-year identity does not close: cash inventory spend ($57.5M) vs bills gross + direct − all credits ($53.0M) leaves ~$3.3M unexplained even accounting for A/P swing (−$0.77M) and un-reclassified duplicates (~$0.4M). Something in the flow is not yet understood — DO NOT flip the engine until it is.

## The decisive test (next session)
Canon, full-year 2023 (pre-ACH, clean): pull every Canon Bill, VendorCredit, BillPayment, and feed Purchase; make every dollar sit in exactly one bucket (kept-inventory, returned, rebated, paid, owed). That determines the correct purchases-received netting rule. Then re-run the 3-year identity; it must close before deploying.

## The conduit principle (Chris articulated it 2026-07-17; this is the design rule)
Chris: "it is just passing on credits to boise… we might be billed a large number but we get credits that we use so cash never moves." Worked example proving him right: Canon bills $100 (Boise product) with a $30 rebate belonging to Boise → we pay Canon $70 cash, invoice Boise $100, settle $30 by credit memo, Boise pays $70 cash. Cash view: 70 out / 70 in — profit-correct with zero noise. Accrual view is only correct if the $30 appears on BOTH sides. Same wash holds for used gear bought from Boise via credit memo (counts capture the gear; income drops by its value) and online territory fees.
**Consequences:** (1) INCOME STAYS BANK-LANDED — the $579k credit-memo exclusion is correct, not a gap; do NOT add it as income. (2) The cost side must be equally net: bills-gross purchases (current D31 state) take the conduit's hit without its offset — the purchases line MUST net used vendor credits. Chris's cash principle argues FOR the netting fix, with the exact rule pending the Canon tie-out (pass-through vs kept credits vs returns).

## Resolved side-questions
- Accountant's 12/31/2025 inventory ($2,560,218): SLC-only books + possible write-downs (Chris, 2026-07-17) — not a discrepancy to chase; his company-wide counts stand for the app.

## Calibration targets (Chris, 2026-07-17)
- "1 million each year" NOI for 2024 and 2025; accountant's 2025 = $1,081,232.95.
- Income side is SETTLED: +$579,213 (2025) of credit-memo-settled Boise revenue is real and currently excluded (bank-landed rule); decide whether to add an explicit "settled by credit memo" income line.
- Counts: Chris confirms his are accurate company-wide; accountant's 12/31/2025 differs ($2,560,218 vs $2,783,704) — ask what theirs is based on.
- Bank-cash reality check (real banks only): 2023 +$704k, 2024 +$252k, 2025 +$387k, H1-26 +$317k; with owner take-outs and inventory growth → rough earnings ≈ $1.09M / $0.85–0.9M / $1.2–1.5M / $0.96M.
