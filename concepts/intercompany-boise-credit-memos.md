# SLC ↔ Boise intercompany structure and the credit-memo ledger

Source: Chris's explanation, conversation 2026-07-14 (his words paraphrased).

## The structure
- SLC (this company) **purchases ALL inventory** for both locations and **claims all manufacturer rebates**.
- Boise pays SLC for inventory two ways: **cash transfers** and **credit memos**.
- Credit memos issued to Boise cover: (a) **online sales credited to Boise** (orders SLC fulfills but Boise gets credit for), (b) **territory sales**, (c) **rebate pass-throughs** (Boise's share of manufacturer rebates SLC claimed), (d) used-gear settlements. None of these appear as Boise transactions in this QBO file.
- Account **11300 "Credit Memos"** is an asset: SLC's running ledger of manufacturer rebate credit. Unused balance pays for future inventory.

## H1 2026 numbers
Booked intercompany revenue from Boise ≈ **$1,207,430** = cash actually received **$802,438** + credit-memo settlements ≈ **$405k**. The $569,397.82 of customer payments landing in 11300 (see bank-landed-income-rule) is the Payment side of this; it matched Chris's credit-memos CSV exactly.

## Open items (parked by Chris — roadmap #5)
1. Audit that issued credit memos match the structure above ("That would be worth auditing tho").
2. Unexplained discrepancy: 11300 balance-sheet balance ~**$221k** vs transaction-report totals ~**$1.15M**. Likely bookkeeping, not app logic — but unresolved.

## Lesson
Intercompany "revenue" here is partly non-cash. Any cash-based income metric must treat credit-memo settlements as NOT income (no cash), while the accounting P&L may still recognize them. This is the single biggest cash-vs-books gap in the file.
