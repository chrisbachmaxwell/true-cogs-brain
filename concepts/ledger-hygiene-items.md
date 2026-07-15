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
| "ACH" account | **−$13,062,007.66**, frozen since 2025-12-31 | Bank-typed clearing account, never reconciled; absorbed −$4.73M net outflows in H2 2025 alone, making any range crossing H2 2025 show phantom losses — see [ach-clearing-account-h2-2025.md](ach-clearing-account-h2-2025.md). **Top-priority bookkeeper item.** |
| "AX Purple" | Typed as **liability**, behaves like a card/clearing account | Wrong account type |
| Shareholder Distributions `31400` | One **$30,000 deposit** coded here | Possibly miscoded — Chris to verify |
| Credit Memos `11300` | Balance ~$221k vs transaction report ~$1.15M | See [intercompany-boise-credit-memos.md](intercompany-boise-credit-memos.md) |

## Why the app is (mostly) unaffected
Income, COGS, and reconciliation are all traced from actual cash transactions (bank-landed rule), not from these balances. Only the **cash-flow (balance-sheet movement) report** reflects them — which is by design, since it shows the books as they are. **Exception:** the Bank-typed "ACH" account pollutes cash math for any range crossing H2 2025 (transactions booked against it count as bank-landed/bank-funded) — see the linked page; 2026 ranges are clean because it froze at year-end 2025.
