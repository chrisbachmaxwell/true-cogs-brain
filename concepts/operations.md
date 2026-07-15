# Operations — deploy, env, sync, cache

**As of 2026-07-15.** All commands run from the code repo root (`true-cogs`).

## Where things live
- Code: `chrisbachmaxwell/true-cogs`, branch `claude/pictureline-inventory-cash-tracker-otub8b` (PR #1, draft). `main` is a seed commit only.
- Hosting: Railway project `456f070c-f728-434f-9e8e-56923d776bc6`, service `web` → `https://web-production-8c8c0.up.railway.app`, Postgres plugin attached.
- Deploys are **from the working tree** via CLI, not git-triggered.

## Build & verify (must pass before ANY push or deploy)
```bash
npm run build && npm test        # tsc + 28 unit tests, all must pass
```

## Deploy
```bash
railway up --detach              # needs RAILWAY_API_TOKEN env var if CLI session expired
```
Build takes 3–5 min. Verify the deploy landed by probing a **static asset marker**, never a computing endpoint (see [deploy-race-stale-recompute.md](deploy-race-stale-recompute.md)):
```bash
curl -s https://web-production-8c8c0.up.railway.app/app.js | grep -q '<known new string>' && echo LIVE
```

## Environment variables (Railway service)
`QBO_CLIENT_ID`, `QBO_CLIENT_SECRET`, `QBO_REDIRECT_URI`, `QBO_ENVIRONMENT=production`, `TOKEN_ENCRYPTION_KEY`, `DATABASE_URL` (plugin), `QBO_INVENTORY_ACCOUNTS=11900,11901`, `QBO_RETAIL_INCOME_ACCOUNTS=all`, `QBO_SALES_TAX_ACCOUNTS=21900`, `QBO_DIRECT_COST_ACCOUNTS=51300,50600,50200`, and (pending) `RESEND_API_KEY`, `AUTH_ALLOWED_EMAILS`, `AUTH_FROM_EMAIL`. Set with:
```bash
railway variables --set KEY=value
```

## Data freshness
- Sync: boot (+15s), every 12h, or `curl .../api/sync`. Status incl. staleness: `curl .../api/status`.
- Caches: closed months forever, current month 1h; force one range: append `&refresh=1` to any report API call. Saving an inventory count auto-purges statement caches.

## QuickBooks connection
- Re-auth if disconnected: visit `/connect` as an admin of the QBO company. `/callback` handles the return. Tokens refresh automatically ~5 min before expiry; rotating refresh tokens are persisted.

## Useful audit endpoints (all read-only, gated by auth when enabled)
`/api/pnl-statement?start&end` · `/api/bank-flow` · `/api/cash-flow` · `/api/summary` · `/api/inventory-spend?month=` · `/api/accounts` · `/api/balance-sheet` · `/api/deposit-lines` · `/api/payments-audit` · `/api/inventory-counts`
