# The bank-landed income rule — and the $569k lesson

Source: conversation 2026-07-14; commits `44400fa`, `d1b74eb`, `0bc9e76`.

## The rule
Income = customer money that **physically entered a Bank-type account** in the period, counted **tax-inclusive at full value**:
- POS/online Deposit income lines (bank-landed)
- Customer Payments and SalesReceipts whose deposit account is Bank-type, at full `TotalAmt`
- minus bank-landed customer refunds

Customer payments that settle into NON-bank accounts are excluded and surfaced as warnings.

## The dead end that produced the rule
After adopting "full-value invoice payments" (Chris: "we've already accounted for the expenses of those items"), H1 income came out **$13.71M — more than total bank inflows ($13.35M)**, which is impossible for a cash income definition. A payments audit (`/api/payments-audit`) found **$569,397.82 of customer payments settled into the Credit Memos asset (11300)** — book settlements where no cash moved (manufacturer rebate credit applied to Boise invoices). Cross-checked exactly against the credit-memos CSV Chris uploaded.

**Fix:** only count payments landing in Bank-type accounts. Result: income $13,237,800.63, which decomposes exactly into deposit income lines + bank-landed payments, and the full bank tie-out closes (income + offsets + equity/misc = $13,351,607 total inflows).

## Lesson
Any income definition must satisfy the invariant **income ≤ total bank inflows**. When it doesn't, audit where payments "landed" — QBO lets payments settle into arbitrary asset accounts.

## Why the app's revenue ≠ QuickBooks' P&L income (Chris asked 2026-07-17)
Same YTD range (2026-01-01 → 2026-07-17), both true, different questions:
- **QBO accrual income $14,620,880.99** (40100 Retail Sales $13,214,772.14 + 49100 Boise intercompany $1,207,430.22 + Services $168,640.85 + small rows) — booked on the SALE/INVOICE date, whether or not paid.
- **App "Money in" $14,344,993.23** (deposits $13,313,032.00 + bank-landed invoice payments $1,031,961.23) — counted the day cash LANDS in a real bank; net revenue after the $632,030.98 tax-remittance deduction: $13,712,962.25.
Bridges: (a) app "deposits" vs 40100 differ ~$98k = card-settlement lag (late-Dec holiday sales landing in Jan, minus mid-July sales not yet settled); (b) the app's one revenue number FOLDS IN Boise + services, so comparing it to the 40100 row alone makes the app look ~$1.1M "bigger" — apples to a fruit basket; (c) QBO income includes $569,397.82 of payments settled into non-bank accounts (store credit / credit-memo settlements) the app excludes, plus invoices still unpaid; (d) the app deducts remitted sales tax from revenue, QBO's income section doesn't (tax rides inside 40100 via Lightspeed). Diagnostic endpoint for this comparison: **`/api/books-pnl?start&end`** (read-only, added 2026-07-17).
