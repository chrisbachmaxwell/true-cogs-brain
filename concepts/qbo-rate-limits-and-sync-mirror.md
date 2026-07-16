# QBO rate limits, caching, and the local mirror

Source: commits `f06e810`, `230abd1`, `964c53b`.

## Rate limits (hit them on day one)
Production QBO allows ~500 requests/min; the first full trend load got 429s. Fixes, in layers:
1. **Shared throttle queue**: 150ms minimum gap between ALL QBO calls + exponential backoff on 429 (max 5 retries) — `withThrottleAndRetry` in `src/qbo.ts`.
2. **Monthly result cache** (`monthly_cache` table): closed months cached forever, current month 1h TTL, `?refresh=1` bypass. Keys carry a view prefix (`pnl:`, `stmt:`, `cf:`, `bf:`) and account-selection/range suffix.
3. **In-flight dedupe**: concurrent identical range requests share one computation (added after the deploy-race incident stacked dozens of recomputes).

## The mirror (the big win)
`src/sync.ts`: 10 transaction entity types mirrored into Postgres `qbo_txns` (JSON + date index). Full backfill once; incremental sync via `MetaData.LastUpdatedTime >` last sync. Runs at boot (+15s), every 12h, and on `GET /api/sync`. `makeLocalApi` serves date-range queries from SQL when the mirror is <26h fresh (`isStoreFresh`); `getBill` backfills misses from the remote; QBO **report** endpoints (P&L, balance sheet) always go remote — they're not mirrorable.

Effect: full-year recompute dropped from ~30 rate-limited minutes to seconds.

## Deletions (fixed 2026-07-15)
Hard-deleted transactions are INVISIBLE to incremental sync (no LastUpdatedTime event) — deleted rows lingered in the mirror and kept counting. Surfaced during the ACH cleanup: Chris deleted feed expenses, but they still appeared as spend and as cleanup to-dos. Fix: **full syncs now purge mirror rows QBO no longer returns** for the window. During active books-cleanup, run `/api/sync?full=1` (not incremental) before regenerating checklists or trusting spend numbers.

## Gotchas
- Pagination: STARTPOSITION/MAXRESULTS; node-quickbooks criteria arrays with field/value/operator.
- `QBO_MINOR_VERSION=75`.
- Mirror date filtering was verified exact against remote (depositTotal matched to the cent) — if local vs remote numbers ever diverge, check sync freshness first, not the query logic.
