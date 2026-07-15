# 2026-07-15 (night) — TTM "loss" investigated: ACH clearing account + count-boundary fix

Chris's screenshot: Jun-30-2025 → Jun-30-2026 dashboard showed NOI −$4.12M vs H1 2026's +$714k. He asked "go check it out for yourself and figure out what is going on."

## Findings (all live-verified, see concepts/ach-clearing-account-h2-2025.md)
1. **Primary: the Bank-typed "ACH" clearing account** absorbed −$4,732,034.78 of net booked outflows entirely within H2 2025, then froze to the cent at 2025-12-31 (zero movement in 2026). Real banks barely moved over the TTM (Zions −$137k). Any range crossing H2 2025 therefore shows a phantom cash loss. 2026 ranges are clean — which is why all the H1 validations held.
2. **Secondary (real bug, fixed):** a range starting 2025-06-30 took its beginning count from 2024-12-31 even though Chris had entered a 2025-06-30 count — `countAsOf(dayBefore(start))` excluded counts dated ON the start day. Fixed: counts dated exactly on a multi-day range's start now serve as the beginning value; statements warn when either count sits >20 days from its range edge. (~$98k of the discrepancy.)
3. Chris has entered year-end counts 2021-12-31 → 2024-12-31 plus 2025-06-30 — the historical-counts homework is largely done.
4. Also observed: the books' Material Inventory (11900) BALANCE swung ~$65M over the TTM window per balance sheet — book balance is meaningless vs physical counts; already ledger-hygiene territory.

## Decisions honored
Did NOT exclude ACH from the bank set unilaterally — that would silently change validated numbers and strip any real ACH-landed income. Recorded as a bookkeeper-first question (what are the H2-2025 ACH entries?), with a configurable `QBO_BANK_ACCOUNTS` as the possible app-side follow-up once answered.

## Next
- Chris/bookkeeper: identify the H2-2025 ACH activity (top ledger-hygiene item now).
- Until then: treat any statement crossing Jul–Dec 2025 as books-polluted; calendar-2026 and 2022–2024 ranges unaffected by this specific issue (2022–24 have their own caveats — goal 05).
