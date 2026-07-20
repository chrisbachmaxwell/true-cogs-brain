# Working-capital shift: paying vendors upfront for early-pay discounts (analyzed 2026-07-19)

Chris's hypothesis: around mid-2023 Pictureline moved from paying inventory on 30/60-day terms to
paying UPFRONT (at order/ship) to capture vendor early-pay discounts (Canon ~2-3%). This converts
vendor float (an interest-free "loan", ~$800k) into cash tied up in inventory — part of "where did
the cash go." New endpoint `/api/discount-shift-analysis` traces it by quarter.

## Findings — the shift is REAL and ~$800k, but the timing is LATE 2024, not mid-2023
**A/P (vendor float), from balance sheet (account 54 "Accounts Payable (A/P)"):**
| Date | A/P |
|---|---|
| 2022-12-31 | $1,450,787 |
| 2023-06-30 | $1,139,115 |
| 2023-12-31 | $1,031,958 |
| 2024-06-30 | $1,026,701 |
| **2024-12-31** | **$115,124** ← collapse |
| 2025-06-30 | $326,808 |
| 2025-12-31 | $682,747 |
| 2026-06-30 | $766,540 |

Held ~$1.0-1.45M through 2022-mid2024, collapsed to $115k in H2-2024 (~$900k unwind, matches Chris's
~$800k), partly rebuilt to ~$767k. **Payment speed** confirms: ~25-day terms (5-15% paid within a
week) through 2023-mid2024 → ~16-17 days (~30% within a week) from Q4-2024. **Vendor discounts
captured** (accounts 1150040007/8/9, COGS contra): ~$0 through 2024, then $7,721 (Q4-25) → $65,789
(Q1-26) → $117,985 (Q2-26) — ramping to a ~$470k/yr run rate.

CONCLUSION: the "pay upfront / capture discounts" behavior is a LATE-2024-into-2025 move in the data,
not May-2023 as remembered (asked Chris to reconcile — decided in 2023 but rolled out late 2024?).

## Where the cash went (answers "part of me thinks we made more money, part thinks $800k paid bills upfront")
BOTH true: (1) ~$900k cash paid down the vendor float in H2-2024 (their free loan → your money out);
partly rebuilt so permanent tie-up smaller now. (2) Inventory grew (counts ~$2.8M end-25 → $3.27M
mid-26) — cash converted to stock. (3) Discounts are real new profit (lower COGS), now ramping fast.
Cash didn't vanish: it's in inventory + faster vendor payments, and the discount margin drops to profit.

## Data-quality notes
- Book inventory accounts (11900/11901) on the balance sheet are BROKEN (run to $57M nonsense) — use
  physical counts. This is the known reason COGS uses counts.
- Derived A/P (cumulative bills − bill-payments) is WRONG — distorted by ~$4.75M of vendor credits
  applied without a cash BillPayment; endpoint uses real balance-sheet A/P instead.
- 2022 bill data is sparse (bill usage ramped late 2022) → early-quarter timing unreliable.

## CORRECTION (Chris: discounts were tracked in "Discounts/Refunds Given" before 2026)
My "late 2024, not 2023" conclusion was WRONG — I only scanned the new "Vendor Discounts" COGS
accounts (which the accountant only started using in 2026). Chris: the accountant tracked vendor
early-pay discounts in **"Discounts/Refunds Given" (acct 58, Income)** through 2025, then moved them to
dedicated Vendor Discounts accounts (50551 SLC / 50552 Boise) in 2026. Reading the RIGHT account (via
the accrual P&L) the timeline CONFIRMS Chris's ~May-2023 memory:

| Year | Vendor early-pay discounts captured |
|---|---|
| 2022 | ~$0 (−$9,851, noise) |
| **2023** | **$134,243** ← started; quarterly: Q1 $14,575 → **Q2 $43,318 (≈May)** → Q3 $37,246 → Q4 $39,104 |
| 2024 | $205,837 |
| 2025 | $254,537 (Discounts/Refunds Given) + $7,721 (new Vendor Discounts) |
| 2026 H1 | ~$142,000 (mostly moved to Vendor Discounts SLC $140,961) |

RECONCILED STORY (both Chris's memory AND the A/P data are right): early-pay discounts **started Q2
2023** (~May, ~$40k/qtr, growing) — a gradual rollout on some vendors. A/P float declined gradually
through 2023 ($1.45M→$1.03M) as they paid faster, then **collapsed in H2-2024** ($1.03M→$115k) when the
program scaled to most vendors. Payment speed: ~25 days (2023) → ~16-17 days (late-2024+). So: discounts
2023-onward (Chris ✓), biggest cash/working-capital impact late-2024. Total discounts captured 2023-2026
≈ $740k of real margin. Endpoint fixed to read the P&L discount accounts (old + new). NOTE: acct 58
"Discounts/Refunds Given" historically also held some customer refunds, so pre-2023 figures are noise;
the 2023+ growth is the vendor-discount program.
