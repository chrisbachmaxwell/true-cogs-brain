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

## Feed-refunds rule (bug found and FIXED 2026-07-17, same session)
Chris drilled into "POS & online deposits" and compared to QBO's 40100 row — off by $63,864.17. Cause: **customer refunds paid OUT of the bank as feed expenses coded to an income account** (PayPal/eBay/Affirm refunds). QBO nets them inside the income row; the app counted deposits but never subtracted these, overstating income and NOI by their exact sum every year: 2024 $82,277.78 (32 txns) · 2025 $48,403.89 (19) · 2026 YTD $61,864.17 (31). Fix: `purchaseIncomePortion()` — bank-funded Purchase lines coded to any tracked income account are deducted as "Less: customer refunds paid from the bank", with an itemized sum-checked drill-down (`?line=feedRefunds`); Credit=true reverses sign; statement/pnl cache generations bumped (`pnl2:`/`stmt2:`). The income invariant is now: **app deposits − feed refunds ≈ QBO 40100** (remaining gap = rare bills miscoded to income, which move no cash — flagged, not deducted).

## Full income-account audit (2026-07-17, all accounts × 2024/2025/2026)
Only three non-deposit patterns exist in the income accounts, all understood:
1. Feed refunds in 40100 (above — fixed).
2. **"Discounts/Refunds Given" (+ "Discounts given"): thousands of negative Bill lines** — vendor purchase discounts booked as income ($205,837 in 2024, $254,537 in 2025, $2,258 YTD-26). No cash moves on the discount line; the app already reflects discounts by paying less cash (D4 positive-line allocation). Books put them in income, app effectively nets them in COGS — GP matches, lines differ. NO ACTION.
3. **74200 Miscellaneous income (Other Income): −$245,770 net in 2026**, recurring −$50–82k/month Feb–Jun, from invoice-side documents (credit memos / item lines — invisible to the account-detail endpoint, which scans AccountRef lines only). Moves no cash, so the app's numbers are unaffected. SOLVED (Chris, 2026-07-17): these are credit memos SLC issues to Boise — rebates passed through (SLC buys all inventory, so rebates land in SLC), online territory sales, used-gear trade-ins bought from Boise — which reduce what Boise pays SLC each month for inventory cost. The app reflects them automatically as smaller bank-landed Boise payments. Books-side cosmetic quirk: the memos net against 74200 instead of 49100 Inventory sold to Boise, so 49100 reads ~$246k high and Misc income negative; totals correct. One-off oddity also flagged: a $2,000 Bill (SLCC, 2026-03-30, txn 55672) coded to 40100 Retail Sales — books-only miscode.

