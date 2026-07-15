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
