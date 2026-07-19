# Bank-outflow & credit-memo audits (2026-07-19)

Two audits Chris requested to prove the P&L isn't overstated by money leaving the bank
that never shows up as a cost.

## 1. Credit-memo audit (`/api/credit-memo-audit`) — are credits sitting unapplied?
Answer: **No, credits move.** As of 2026-07-19:
- **Vendor credits** (the rebate pipeline — money vendors owe us, applied against their bills):
  2,547 issued totaling **$4,750,503**; only **9 still open = $7,034.65** unapplied. Only 4 are
  older than 180 days, totaling **$510.70** (trivia: Hahnemuhle $23.20, tiny Tilta amounts).
- **Customer credit memos**: 9 issued ($265,770), **$0 open** — all fully applied.
- Verdict: ~99.85% of vendor credits are applied; nothing meaningful is stuck. The rebate-as-A/R
  invoices (see accounts-receivable-audit.md) get settled by these vendor credits promptly.

## 2. Outflow audit (`/api/outflow-audit?start&end`) — does every dollar out land as cost or a known non-expense?
Classifies every dollar leaving the real cash pool (Zions checking + 2 savings + ACH clearing)
by the account it's ultimately coded to. Bill-payment attribution scaled by cash fraction (D33:
credits aren't cash out). **Unclassified = $0.00 for both 2025 and 2026 — NO hole.**

| Bucket | 2025 | 2026 H1+Jul | On the P&L? |
|---|---|---|---|
| Inventory / COGS | $16,296,441 | $11,159,730 | yes (via counts) |
| Operating expenses | $615,094 | $297,471 | yes |
| Customer refunds | $48,404 | $63,864 | yes (contra-income) |
| Transfer between own accounts | $8,233,240 | $152,343 | no (internal) |
| Loan / payroll-tax / liability paydown | $1,603,365 | $1,025,085 | no |
| Credit-card paydown | $1,213,834 | $855,862 | no |
| Owner distributions / dividends | $826,975 | $120,000 | no |
| Owner estimated taxes (1040-ES) | $151,500 | $81,700 | no |
| Capital assets | $36,795 | $38,411 | no |
| **Unclassified (the hole indicator)** | **$0.00** | **$0.00** | — |

### The payroll point (why cash-expenses << P&L expenses is CORRECT, not a hole)
Visible cash operating-expense out (2025 $615k) is far below P&L operating expenses (2025 $2.94M).
The ~$2.3M difference is **payroll + Sales-Tax-Center**, which leave the bank with **no QuickBooks
transaction** (paychecks/tax-center are processed outside QBO — the long-standing API-invisible gap,
see qbo-api-invisible-transactions.md). That money is STILL on the accrual P&L as an expense, so it
is fully counted. The gap goes the SAFE direction: expense is recorded even though the cash is
invisible → profit is **not** overstated.

### Conclusion for Chris
Every dollar leaving the bank is accounted for. The cost dollars (inventory, expenses, refunds) are
on the P&L; the non-cost dollars (transfers, debt paydown, owner draws, taxes, capex) correctly are
not; and the only invisible outflow (payroll) is also on the P&L. There is no leak that would
overstate profit. New QboApi surface used: queryRaw, listVendors, getBills.
