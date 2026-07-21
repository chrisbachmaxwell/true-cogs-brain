# 2026-07-19 — card fixes executed, 2025 traced to 99%, P&L made fast

Continuation of the 07-17 marathon; the full narrative lives in
[2026-07-17-cleanup-run-and-2024-taxes.md](2026-07-17-cleanup-run-and-2024-taxes.md), **Addenda 16–20**:

- **16** — twin-matcher falsifies the double-payment theory; real diagnosis = missing charges (2022 pre-feed + 2024 feed collapse); 3 true twin candidates; repair = one truing JE (parked on Chris's todo).
- **17** — "where the money is": residual decoded to the Amex black box + payroll timing; the card-side/bank-side "Credit Cards"-wash booking scheme decoded; closing data = Amex statement balances.
- **18** — 2025–26 verified COMPLETE month-by-month; the 10 miscoded payments enumerated; QBO does NOT backfill feed gaps (>90 days lost unless CSV-uploaded).
- **19** — Chris approved; engine recoded 9 payments ($358,305.61), verified penny-exact; money-map now measures 2025+ card movement from transactions → **2025 residual $16,909 (99.0% traced)**; H1-2026 $206,813 with known suspects.
- **20** — P&L cold load 33.3s → 1.6s (batch bill prefetch) + post-sync cache pre-warming; NOIs byte-identical; checks 10/10.

**Waiting on Chris:** Dec-2024 statement balances for both Amex cards (Platinum predicted $364,944.19) · truing JE with accountant · bookkeeper fix for the double-booked bounced autopay ($76,640.30). All on roadmap item 3.

## Addendum: money-map readability (Chris review 2026-07-19)
Chris on the YTD /money page: (1) "bank went from 300k to 100k" and (2) the measured card rows show "start 0 end 0 but 180k."
- **Bank $317k→$109k is REAL and explained by July:** end-June Zions $1,013,162 → end-July $805,072 (−$208k). In July they paid down A/P $766,540→$194,438 (−$572k of vendor bills) and grew inventory +$103k. YTD the cash turned into inventory (+$586,596) and payables paydown; not missing. The report window simply now includes July.
- **"$0→$0 but $180k" was a display bug I introduced:** the measured card rows carry before/after=null (book balance untrusted); `usd.format(null)` rendered $0.00. FIX (commit cdbbf61): money.html renders null balances as "—"; measured rows relabeled plain-English ("Amex Platinum — payments minus charges this period" / "Card payment clearing (timing between the two feeds)"); added open-period caveat that the current-year Platinum paydown (−$181,457) is overstated ~$76,640 by the July bounced-autopay double-booking (todo #3) until the bookkeeper removes one copy. Verified live at /money (note: page is served at /money, NOT /money.html — /money.html is an unregistered route). NOIs unaffected; residual YTD $46,500 (96.4% traced).

## Addendum: A/R audit (Chris: "the A/R may not be real")
Built /api/ar-audit (read-only: open invoices grouped by customer, fuzzy-matched to vendor list, with line descriptions). Confirmed Chris's hypothesis exactly: of $226,424.99 A/R, **$178,787 (79%) is vendor rebate/co-op/instant-rebate claims** (Canon 80,698 · Nikon 40,517 · Sony 28,134 · …; descriptions "IR Claim", "MDF Funds", "MVP Savings", "Spiffs", "Price Protection"), **$47,638 (21%) is Boise intercompany**, **$0 real customer debt**. No profit distortion: app counts bank-landed cash only, rebates settle by credit memo (conduit principle) and correctly surface as lower COGS via the D33 cash-fraction logic. Money-map A/R note updated to say so. New concept page: accounts-receivable-audit.md. New QboApi methods: queryRaw, listVendors.

## Addendum: July autopay resolved (Chris was right) + measured-card sign bug fixed
Chris: "the autopay shows twice because we paid twice but Amex returned one and sent money back." CONFIRMED and the books are complete: CCP 60291 (Jul 2 $76,640.30 out) + Deposit 60301 (Jul 3 $76,640.30 back into Zions, "AMEX EPAYMENT REF#…350072", coded to card 63) + CCP 60392 (Jul 6 $76,640.30 out) = net ONE payment. **Nothing to delete; no bookkeeper action** — my earlier "double-booked, remove one copy" caveat was WRONG. REMOVED from roadmap item 3.
BUT this exposed a real bug in registerMovement (money-map measured cards): a Deposit coded to a credit card is a returned/refunded payment that RAISES what's owed, but the code subtracted it like a paydown (`net -= amount`). July's return was thus counted as a THIRD paydown, inflating measured Platinum paydown by 2×$76,640 = $153,281. FIX (commit 699d33d): deposit-to-card now `net += amount`. VERIFIED: YTD measured Platinum −181,457 → **−28,176.39, exactly matching the QBO balance-sheet change** (2026 has no feed gaps, so measured should equal book change — it now does). Side effect: YTD-2026 residual corrected from an artificially-low $46,500 (the bug was absorbing real residual into fake paydown) to the honest **$199,781**, consistent with H1's $206,813. 2025 unaffected (residual $16,909; no returned payments in range). Also removed the now-moot open-period caveat text; card detail note now says returned payments are counted correctly.
Credit-memo / A/P explanation delivered to Chris (see below in chat): A/P = unpaid-bills balance snapshot (Start=owed at range start, End=owed at range end); Credit Memos = Other Current Asset holding account (id 1150040003), both in the info-only "owed" bucket, excluded from the profit proof.

## Addendum: outflow & credit-memo audits (Chris: "does money leaving the bank show up as COGS/expenses? if not, PNL is overstated")
Built /api/credit-memo-audit and /api/outflow-audit. RESULTS: (1) credits move — vendor credits 2,547 issued $4.75M, only 9 open ($7,034.65), $510.70 >180d; customer memos $0 open. (2) Outflow **unclassified = $0.00** for 2025 AND 2026 — every dollar leaving the real cash pool (Zions ck+2 savings+ACH) is classified. Cost buckets (inventory/COGS, opex, refunds) are on the P&L; non-cost (transfers, card/loan paydown, owner draws, 1040-ES, capex) correctly aren't. THE PAYROLL POINT: visible cash opex ($615k 2025) << P&L opex ($2.94M) because ~$2.3M payroll + STC leave the bank invisibly (no QBO txns) yet ARE on the accrual P&L → counted, safe direction, profit NOT overstated. Fixed a D33-style over-attribution bug mid-build (bill-payment lines are blended cash+credits; scaled by cashFraction → unclassified went from −$1.2M to $0.00). New concept: outflow-and-credit-memo-audits.md. New endpoints all read-only.

## Incident 2026-07-21: P&L page "upstream error is not valid JSON" — fixed
Chris's /pnl (custom Jan1–Jul21) hung until the Railway edge returned plain-text "upstream error";
jfetch's res.json() then threw the raw parse error at him (violating the no-raw-errors rule). ROOT
CAUSE: the per-bill-line console.log in inventorySpend bucket 1 — a multi-year statement compute (the
since-partnership money-map ranges) emits tens of thousands of synchronous stdout writes; stdout
backpressure stalls the Node event loop → ALL requests hang past the proxy timeout. FIXES (commit
pushed + deployed): (1) removed the per-line log (transactions[] is the audit trail); (2) jfetch now
reads text first and translates non-JSON bodies to "The server is taking longer than usual… press
Refresh". VERIFIED: exact failing call → 200 in 1.68s cold / 0.74s warm, valid JSON; jfetch marker
live. LESSON: never per-item console.log inside range-sized loops — log volume is a DoS on your own
event loop (stdout backpressure blocks synchronously).
