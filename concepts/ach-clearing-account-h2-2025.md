# The "ACH" pseudo-bank account — why mid-2025 ranges show phantom losses

Source: investigation 2026-07-15, triggered by Chris's screenshot (TTM Jun-2025 → Jun-2026 showing NOI −$4.1M). All figures verified against live balance sheets.

## The facts
- QBO has an account named **"ACH"**, typed **Bank**, that is a clearing account for the ACH payment rail. It has never been reconciled.
- Balances: 2025-06-30 **−$8,329,972.88** → 2025-12-31 **−$13,062,007.66** → 2026-06-30 **−$13,062,007.66**.
- It absorbed **−$4,732,034.78 of net booked outflows entirely within H2 2025**, then **froze to the cent on 2025-12-31** — zero movement in all of 2026 (bookkeeping regime evidently changed at year-end).
- Over the same TTM window the REAL bank accounts barely moved (Zions checking 8882: −$137k; both savings: flat).

## The full contents (from Chris's QBO export — `raw/2026-07-15-ach-account-transactions.xlsx`)
Exactly **485 transactions, all "Bill Payment (Check)" against A/P, ZERO money ever in**, spanning 2024-07-15 → 2025-12-29:
- Canon USA **−$5,982,656.78** (162) · Sony–TD Synnex **−$4,152,526.17** (151) · Nikon **−$2,790,831.99** (152) · Ingram Micro −$89,415 · small others.
- By half: 2024-H2 −$4,328,903.04 · 2025-H1 −$4,001,069.84 · 2025-H2 −$4,732,034.78.
- **The polluted window is therefore Jul 2024 → Dec 2025** (not just H2 2025 — the earlier balance-sheet framing understated it; the account was BORN mid-2024).

## Chris's explanation (2026-07-15)
"they should have been matching payments to bills but they weren't, they are now. a payment would come in and they wouldn't match it" — i.e., the real Zions bank-feed withdrawal was not matched to the bill; instead a manual Bill Payment drawn on the fake "ACH" bank was recorded to clear A/P, leaving the feed item to be booked separately. The process was fixed at year-end 2025 (hence the freeze).

## Quantifying app impact
Vendor payments to the same big-three vendors appear via TWO recording paths in the polluted window (verified Oct 2025: Canon as BillPayment/Check AND as Purchase/Cash in the same month). `/api/spend-by-funding?start&end` (built 2026-07-15) breaks inventory spend down by funding account. Measured inventory-attributed ACH-funded spend: 2024-H2 $4.70M · 2025-H1 $4.49M · 2025-H2 $5.21M · 2026-H1 $0.

## Year-split limitation (found 2026-07-15, investigating Chris's 2023–25 annual NOIs)
With the exclusion on, annual NOIs read: 2023 $281,943 (16.3% GP — income/COGS trustworthy, pre-ACH; but 2023 EXPENSES are inflated by up to ~$430k of probable duplicate tax/payroll pulls in account 66000 — see ledger-hygiene-items — so true 2023 NOI is plausibly $500–700k), 2024 $99,062 (13.8% GP — too low), 2025 $1,643,246 (21.75% GP — too high vs clean-2026's ~16%). Twin analysis against the ACH register:
- **Nikon's** ACH payments have NAMED kept twins (kept/ACH ≈ 1.0 in 2025) — clean duplicates.
- **Canon/Sony's** twins are the **"Unknown payee"** entries — bank-feed withdrawals entered with no vendor (~$3.3–3.7M per half, ~200 txns each, ≈ $10.5M over the window). Duplication broadly holds, BUT twin totals don't match ACH totals half-by-half (±$1M/half), and that mismatch sloshes profit between 2024 and 2025.
- **Conclusion: 2024 and 2025 INDIVIDUAL years are not reliable; their SUM is** (2024+2025 NOI ≈ $1.74M, ~17.6% avg GP — sane). No automatic rule can split them correctly; only the bookkeeper cleanup (void ACH payments AND name/match the Unknown-payee feed entries to bills) yields true per-year numbers. Tell the bookkeeper: the "Unknown payee" expenses from Zions are the feed-side twins to match.

## CLEANUP STATE (2026-07-17 — near the finish line)
Balance is down from **−$13,062,007.66 to −$159,135.97**. Done: the 425-row belt (engine + Chris's hand conveyor, all retired), the 21-row `ach-stragglers` engine run ($580,540.37 — wires re-found by analyzing the 37 unmatched payments; report in raw/2026-07-16-ach-unmatched-37.html). Remaining open items:
- **14 payments with no reclassified wire** (~$258,669): the 7 unresolved from the 37-analysis ($164,722.60, incl. the first Jul-2024 payments to Nancy Borowick/Rotolight and three big 2024 Sony payments), 6 loose-match payments whose suggested wires were rejected or weak ($42,499.07 — the Amex and employee-loan false-positive suggestions live here), and Canon 49978 ($51,446.84) whose suggested $50,000 Jan-2026 wire FAILED the inventory-coding check (correctly refused — not a duplicate; straggler manifest row deliberately left pending).
- **8 quarantined belt rows** ($292k) still need Chris's eyes (raw/ conveyor page).
- **Overpayment residue** (~+$102k inside the balance): one entry to 68000 Vendor Overpayment Write-offs once the above resolve.
Then: unset `QBO_EXCLUDED_FUNDING_ACCOUNTS`, redeploy, refresh 2024/2025, re-run /checks.

## THE CLEANUP STRATEGY (settled 2026-07-16 with Chris — supersedes void-and-match)
Chris pivoted from per-payment void→re-apply-credits→match (too slow, ~470 payments) to **reclassify-to-clearing**: change each Zions-side feed expense's category from Material Inventory to **ACH**. The ACH bill payments STAY (they become the single cost record via the bills); each reclassified expense pays DOWN the ACH balance; reconciliation untouched; no credit re-application. Chris's own summary, which is exactly right: "we use the ACH as a clearing account once to pay the bill and when the expense comes across we then clear it out." Conveyor checklist: raw/2026-07-16-ach-reclassify-conveyor.html (437 expenses, $11.76M, batch-open + auto-check).
**Mid-cleanup warning: 2024–25 statements UNDERCOUNT while this runs** (reclassified twins stop counting AND ACH payments are excluded). Do not trust those ranges until done. **When ACH reads $0.00: remove `QBO_EXCLUDED_FUNDING_ACCOUNTS`, redeploy, refresh 2024/2025** — ACH bill payments then count once, correctly. Residue (overpayments) → 68000 Vendor Overpayment Write-offs (created 2026-07-16) or one JE.

## The mitigation in force (D26 — since 2026-07-15)
`QBO_EXCLUDED_FUNDING_ACCOUNTS=ACH` is set in Railway: the spend engine skips ACH-funded payments everywhere (statement, dashboard, drill-downs, tax, direct costs), statements show "Excluded $X…", and cache keys carry an `xf:` prefix so polluted/corrected results never mix. `/api/spend-by-funding` stays unfiltered as the diagnostic. **When the bookkeeper zeroes the ACH account, unset the env var and redeploy — done.** H1 2026 numbers are identical with or without the exclusion (verified).

## Why this poisons mid-2025 reporting
The app treats every QBO Bank-type account as "bank": income = bank-landed deposits, and bank change = Bank-type balance diff. Purchases (Bucket 1/2) count all BillPayments/Purchases regardless of funding account. In H2 2025 ~$4.7M of net activity ran through ACH that never showed in a real bank, so any range crossing H2 2025 shows a large "cash loss" that no real account experienced. TTM Jun→Jun: booked NOI ≈ −$4.0M while real banks moved ≈ −$137k and physical inventory grew ~$866k.

## Why H1 2026 (and the validated H1 numbers) are UNAFFECTED
ACH is frozen since 2025-12-31 — it contributed exactly $0 to any 2026 range. The H1 2026 tie-outs (income = bank inflows, direct method to the penny) remain valid.

## What is NOT yet known (bookkeeper question — do not guess)
What the H2-2025 ACH entries represent: double-booked vendor payments? unposted settlement inflows? a migration artifact? Until Chris/bookkeeper answers, do NOT "fix" this in the app by excluding ACH from banks — that would also strip any REAL income deposited via ACH in H2 2025 and would silently change validated numbers. The right sequence: (1) bookkeeper identifies/reconciles the H2-2025 ACH entries; (2) only then consider a configurable real-bank list (`QBO_BANK_ACCOUNTS`) as decision material for Chris.

## Rule for agents
A "cash loss" concentrated in a period where a Bank-type clearing account swung is a BOOKS artifact until proven otherwise. Diff every Bank-type account across the window (per-account, not the total) before believing any bank-change number. Suspect Bank-type accounts in this file: **ACH (−$13.1M, frozen), Fraud ($84k), Cash on Hand (negative), Federal Estimated Tax, QuickBooks Checking (−$49k), Employee Benefits, misc, eline2** — only Zions 8882 and the two Zions savings look like real banks.
