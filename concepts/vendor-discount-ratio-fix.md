# Vendor-discount over-attribution: allocation ratio > 1

Source: commit `f06e810`; Chris confirmed the business pattern.

## The bug
Bucket-1 COGS allocates each BillPayment by `inventoryPortion / billTotal`. Pictureline's vendor bills often contain a **negative discount line** ("the bill is larger than what is actually paid… split category" — Chris). Using the bill's NET total as denominator made the ratio exceed 1, attributing more cash to inventory than was paid.

## The fix
Denominator = **`positiveLineTotal`** — sum of positive charge lines only (`src/inventorySpend.ts`). The inventory ratio is then inventory's share of gross charges, and negative discount lines simply shrink the payment being allocated, not the ratio.

## Related invariants in the same engine
- Vendor credits are applied inside BillPayment lines → already net → NEVER subtract vendor credits separately (double-subtraction).
- Journal entries and plain Deposits touching inventory accounts generate warnings, not spend.
- Refund purchases subtract from Bucket 2.

## Rule
When allocating a payment by a document's line composition, always ask: can lines be negative? If yes, ratio denominators must use positive lines only.
