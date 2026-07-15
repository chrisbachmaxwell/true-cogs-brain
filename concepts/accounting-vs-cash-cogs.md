# Accounting-basis vs cash-basis COGS — the methodology

Source: conversation 2026-07-14/15; commits `f6bf371`, `7ddca5d`.

## The two numbers
- **Cash COGS** = cash that left the bank for inventory in the period (two-bucket engine, `src/inventorySpend.ts`). Answers "what did we spend?"
- **Accounting COGS** (matching principle) = beginning inventory + purchases − ending inventory. Answers "what did the goods we SOLD cost?" This is what belongs on a P&L, and Chris confirmed it's "the right" number.

## Why cash COGS alone misled
In H1 2026 Pictureline **grew inventory by $597,053.54** (counts: 2025-12-31 $2,667,236.20 → 2026-06-30 $3,264,289.74). Cash COGS charges that growth against the period even though the goods weren't sold, understating NOI by ~$600k (cash NOI $116,453 vs accounting NOI $713,507).

## How the app computes it
`/api/pnl-statement` uses physical counts from the `inventory_counts` table (entered by Chris on the /pnl page, month-end, all locations combined):

```
cogsTotal = cashPurchases − inventoryChange + directCosts − rebateOffsets
```

- The statement picks the counts nearest the range start/end and displays their dates on the face of the statement.
- **Fallback:** if no surrounding counts exist for a range, the statement shows cash-basis COGS, clearly labeled "cash basis — no inventory counts for this range". This is the ONLY place cash-basis appears (D13 — Chris explicitly rejected showing both).

## Why physical counts, not QBO balances
Boise inventory (11901) value and true on-hand values aren't reliable from QBO cash data alone; Chris committed to entering monthly physical values ("yes I want that. I can add those monthly"). Saving a count purges `stmt:%` cache rows so statements recompute.
