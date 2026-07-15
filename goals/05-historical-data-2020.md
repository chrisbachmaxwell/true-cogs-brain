# Goal 05 — Full history back to January 1, 2020

**Status:** ACTIVE — requested by Chris 2026-07-15 ("I want data all the way back from january 1 2020, then I can add all the inventory levels going back that far").

## What it takes (small — the architecture already supports it)
1. `QBO_SYNC_START=2020-01-01` in Railway env (sync window was 2025-07-01). **Done 2026-07-15.**
2. One full sync (`GET /api/sync?full=1`) to backfill ~6.5 years of the 10 mirrored entity types. Runs server-side through the rate-limit throttle; can take a while on first pull. Incremental syncs stay cheap afterward.
3. Range caps raised: month-based APIs (summary, cash-flow, bank-flow) 24 → 84 months; trend chart shows the last 36 months of a range. Day-granular statement/detail/checks endpoints never had a cap. **Done 2026-07-15.**
4. Chris enters historical inventory counts (see below).
5. Verify: pick a 2020 month, confirm statement + drill-downs + checks work; closed historical months cache forever after first compute.

## Inventory counts for history — the advice given to Chris
**Month-end counts only; no separate beginning-of-year entries.** The statement uses the count nearest each range edge, and one date serves both roles: **Dec 31 of one year IS the beginning count for the next year** — entering Jan 1 separately would duplicate it (and a tiny mismatch between a Dec 31 and Jan 1 entry would create a phantom COGS adjustment).
- Minimum useful set: **every year-end (2021-12-31 → 2025-12-31**, revised after the data-start finding below) — enables clean annual P&Ls for every year QBO actually has.
- Ideal: **every month-end** he has values for — enables monthly accounting-basis P&Ls.
- Where a count doesn't exist, the statement falls back to cash basis for that range, clearly labeled — nothing breaks.

## Done when
- [ ] Full sync from 2020-01-01 completed (`/api/status` sync line shows it; entity counts plausible for 6.5 years).
- [ ] A 2020 range (e.g. 2020-01-01 → 2020-12-31) renders a P&L statement with drill-downs whose sum checks pass.
- [ ] /checks runs green (or explained warns) on at least one historical year.
- [ ] Chris has entered year-end counts back to 2019-12-31 and the 2020 annual statement shows accounting-basis COGS.

## Stop clause
If the full backfill repeatedly fails or rate-limits QuickBooks hard (watch for 429 storms in logs), stop and stagger the backfill year by year rather than hammering; never leave the mirror half-full without noting it in `/api/status` terms in the log.

## KEY FINDING (2026-07-15): QuickBooks history begins JANUARY 2022
The full backfill (52k+ transactions, ~8 min) pulled everything QBO has. **2020 and 2021 are empty** — verified across deposits AND bills/purchases, quarter by quarter (one stray deposit in Q4 2021, likely a setup entry). This is a fact about the company file (probably migrated to QBO at the start of 2022), NOT a sync bug — do not chase it as one. Pre-2022 reporting would require Chris to locate the old system's exports (ask him where 2020–21 books live).

Consequences:
- Earliest useful inventory count is **2021-12-31** (beginning inventory for 2022), not 2019-12-31.
- Historical statements verified working: 2022 full year computes from the mirror (moneyIn $23,859,345.46, cash COGS $17,279,931.89).
- Caveats on old years: sales-tax remittances may trace to $0 (likely paid via the API-invisible tax center back then — the range statement has no balance-sheet fallback, unlike the monthly path); the 2022 books' expense section looks structurally different (opex $11.9M). History is only as clean as the books were — set expectations with Chris before he compares old years to H1 2026 quality.

## Iteration log
- 2026-07-15 (worker): env set (QBO_SYNC_START=2020-01-01), caps raised, deployed; full backfill completed (Deposit:6476 Payment:548 BillPayment:4569 Bill:24599 Purchase:13387 VendorCredit:2545 Transfer:161 JE:37). Data-start finding above; 2022 statement verified live. Remaining: Chris enters 2021-12-31 → 2024-12-31 year-end counts; decide whether pre-2022 matters enough to import from the old system.
