# Sales tax: tax-inclusive income, deducted exactly once

Source: conversation 2026-07-13/14; commit `2502f71`. Validated to the penny.

## The problem
Chris objected to counting sales tax in profit ("it's not our money… it should be in a holding account and our profit should have been less") but ALSO objected to double-handling. Meanwhile Lightspeed POS syncs **tax-inclusive** daily totals into income 40100, so the tax money genuinely arrives inside "income".

## The resolution
1. Count income tax-inclusive as it lands (that's what the bank receives).
2. Deduct **"Less: sales tax remitted"** as contra-revenue — ONCE — using actual remittance transactions traced to the sales-tax account (21900), with a balance-sheet-movement fallback.
3. Never also exclude tax from individual invoice payments (an early version did both — double exclusion — and Chris caught it: full-value payments, D6).

## Validation
H1 2026 remittances traced: **$632,030.98** — matched the sales-tax ledger movement exactly.

## Lesson
With a tax-inclusive POS feed, "exclude tax from revenue" and "deduct remittances" are the SAME adjustment. Do one, never both. Any change to income handling must re-check this single-deduction invariant.
