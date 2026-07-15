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
