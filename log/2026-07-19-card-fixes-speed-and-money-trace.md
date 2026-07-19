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
