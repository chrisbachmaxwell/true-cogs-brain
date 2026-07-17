# Known-bad ledger balances — bookkeeper punch list

Source: balance-sheet review with Chris, 2026-07-14. These are BOOKS problems, not app bugs — the app works around them. Hand to the bookkeeper (roadmap #6). (verify after: 2026-09)

| Account | Symptom | Note |
|---|---|---|
| Sales tax payable | ≈ **−$2.6M** (huge negative liability) | Remittances booked without matching accruals over years; app traces actual remittance transactions instead of trusting the balance |
| Amex card account | ≈ **−$1.95M**, sign appears flipped | Payments/charges likely booked reversed for a period |
| A/P `20200` | Frozen — no movement | Stale; real payables flow elsewhere |
| A/R `11000` | Frozen — no movement | Stale; Lightspeed/invoices bypass it |
| "Fraud" account | **$84k** parked | Unresolved incident balance |
| Cash on Hand | **−$79,568** | Negative cash — impossible; miscoding |
| "ACH" account | was −$13,062,007.66; **−$159,135.97 as of 2026-07-17** after the reclassify cleanup | Bank-typed clearing account used Jul 2024–Dec 2025 instead of matching payments to bills — see [ach-clearing-account-h2-2025.md](ach-clearing-account-h2-2025.md) for what remains (14 unmatched payments, 8 quarantined rows, overpayment write-off). |
| "AX Purple" | Typed as **liability**, behaves like a card/clearing account | Wrong account type |
| Shareholder Distributions `31400` | One **$30,000 deposit** coded here | Possibly miscoded — Chris to verify |
| Payroll Expenses `66000` (2023–24) | was $750k (2023) + $298k (2024) of bank-feed pulls hand-coded as payroll expense | **2023 tax pulls ($563,880.31): FIXED by Chris by hand 2026-07-17** (all 16 recoded to 21900 / State Tax W/H per the checklist; engine verified live and retired them). **2024 pulls ($298,197.82, Apr–Jul): manifest `tax-pulls-2024` built + deployed 2026-07-17, awaiting the run** (4 big → 21900 $273,195.04; 4 small → State Tax W/H $25,002.78). 2025/2026 66000 rows are small non-tax items ($6.2k/$2.4k) — leave. Remaining 2023 judgment rows (IRS pair, JENS, teller, direct-deposit deletes + $48,990.88 transfer, SUI, 401k) still manual. NOTE: recoding SALES-TAX pulls barely moves NOI (the app then deducts them from revenue instead — composition fix); the WITHHOLDING pulls are the true double-counts. |
| Credit Memos `11300` | Balance ~$221k vs transaction report ~$1.15M | See [intercompany-boise-credit-memos.md](intercompany-boise-credit-memos.md) |

## Why the app is (mostly) unaffected
Income, COGS, and reconciliation are all traced from actual cash transactions (bank-landed rule), not from these balances. Only the **cash-flow (balance-sheet movement) report** reflects them — which is by design, since it shows the books as they are. **Exception:** the Bank-typed "ACH" account pollutes cash math for any range crossing H2 2025 (transactions booked against it count as bank-landed/bank-funded) — see the linked page; 2026 ranges are clean because it froze at year-end 2025.
