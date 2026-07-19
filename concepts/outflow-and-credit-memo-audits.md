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

## Follow-up: the invisible-expense gap decomposed (Chris: "payroll is 1.9M, what about the rest?")
The gap between visible bank-cash expenses and P&L operating expenses is NOT all payroll. Added
`reconciliation.expenseBreakdown` to the outflow audit — P&L opex decomposed by how paid:

| 2025 | Amount |
|---|---|
| P&L operating expenses | $2,941,967 |
| — paid from bank (visible) | $615,094 |
| — **paid by credit card** | **$575,583** |
| — payroll & payroll taxes | $1,933,249 |
| — timing overlap (accrual-vs-cash / reimbursement-netting) | −$181,959 |

The "rest" beyond payroll is ~$576k of operating expenses charged to the credit cards (Google/Meta
ads, Shopify, software, travel, trade shows, Auctane shipping) — on the P&L, paid by card not bank
(the bank pays the card down later → the non-cost "credit-card paydown" bucket). The three channels
slightly OVERSHOOT the P&L (~$182k / 6%) from normal accrual-vs-cash timing and the P&L being net of
reimbursements while card charges are gross. Conclusion strengthened: every P&L expense dollar is
covered by an identifiable payment channel (bank + card + payroll); nothing hides; profit not
overstated. 2026 H1+Jul mirror: bank $297k + card $320k + payroll $994k vs P&L $1,502k (overlap −$111k).

## Clean additive expense decomposition (fixed the payroll double-count)
Chris pushed: bank+card+payroll overshot the P&L, worried expenses inaccurate → profit overstated.
The overshoot was a measurement double-count (payroll TAXES are both a "payroll" P&L row AND a bank
tax deposit). Rebuilt the decomposition additively (payroll from P&L rows; non-payroll split by
bank/card/unpaid, excluding payroll accounts from bank/card counting). Now sums to GROSS to the penny:

| 2025 | Amount |
|---|---|
| Gross operating expenses (P&L) | $2,973,144.35 |
| — payroll & taxes | $1,933,249.06 |
| — non-payroll expenses | $1,039,895.29 |
| &nbsp;&nbsp;&nbsp;• paid by bank | $537,221.27 |
| &nbsp;&nbsp;&nbsp;• paid by credit card | $575,286.68 |
| &nbsp;&nbsp;&nbsp;• timing (paid ahead / prior-yr bills) | −$72,612.66 |
| CHECK sum | $2,973,144.35 ✓ (= gross) |

2026 H1+Jul: gross $1,536,807 = payroll $994,333 + non-payroll $542,474 (bank $285,786 + card
$320,399 + timing −$63,711). CHECK exact.

**Basis clarified for Chris:** the P&L is HYBRID — income cash-based (bank-landed), COGS from physical
counts, operating expenses ACCRUAL (from QBO books, booked when incurred regardless of payment method).
Expenses intentionally do NOT tie to bank payments; accrual is more complete (captures card-paid,
payroll, and unpaid-bill expenses). **Profit not overstated:** the timing line is NEGATIVE, i.e.
bank+card paid slightly MORE than the year's booked expense (covering prior-year bills). A hole would
be a POSITIVE leftover (expense booked, no payment); we have the opposite. `reconciliation.expenseBreakdown`
now exposes this for any period.
