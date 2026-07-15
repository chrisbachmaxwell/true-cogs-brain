# QBO API blind spots: payroll and sales-tax-center payments are INVISIBLE

Source: empirical discovery, conversation 2026-07-14; commit `4231978`.

## The fact
The QuickBooks Online API does **not** expose:
- **Paychecks / payroll transactions** (QBO Payroll)
- **Sales-Tax-Center TaxPayments** (remittances made through the tax center)

They appear in the QBO UI and in bank feeds, but no queryable entity returns them. No amount of entity enumeration finds them — we mirrored all 10 available transaction types and they're simply absent.

## Impact
Transaction-by-transaction bank reconciliation can never fully tie out from the API alone. In H1 2026 the gap was **$727,012** (money in $13,351,607 − categorized out $12,225,723 − gap = actual bank change +$398,871). The gap is real payroll + tax-center outflows.

## What we do instead (don't re-derive this)
- `/api/bank-flow` computes the categorized direct-method flows and reports the remainder as an explicit line: "Payroll & tax-center payments (not visible to the API)" — honest, not hidden.
- Sales-tax remittances for the P&L are traced via ledger transactions against 21900 with a balance-sheet-movement fallback (works even though TaxPayment entities are missing).
- Operating expenses come from the books' accrual P&L report (D12), which DOES include payroll.

## Dead end to avoid
Do not try to cash-trace payroll or build "total expenses from transactions" — it will silently miss payroll and understate expenses by millions/year. The only path to itemizing the gap is Chris's bank-ledger CSV (roadmap #4).
