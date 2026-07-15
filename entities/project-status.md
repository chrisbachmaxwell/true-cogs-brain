# Project status — WHAT IS DONE

**As of: 2026-07-15** (verified against `chrisbachmaxwell/true-cogs` @ `7ddca5d`; build passes, 28/28 tests pass)

## Deployed and in use
- Live at `https://web-production-8c8c0.up.railway.app` (Railway project `456f070c-f728-434f-9e8e-56923d776bc6`, Nixpacks, Postgres plugin, healthcheck `/health`).
- Connected via OAuth to Pictureline's **production** QuickBooks Online company (read-only). Tokens AES-256-GCM-encrypted in Postgres; auto-refresh 5 min before expiry.
- PR #1 (draft) on `claude/pictureline-inventory-cash-tracker-otub8b` holds all 30 commits; `main` is an empty seed commit. **Not merged** — the app deploys from the working tree via `railway up`, not from git. (verify after: 2026-08)

## What works (all repo-verified)
| Piece | Where | State |
|---|---|---|
| Cash COGS engine (two-bucket: BillPayments allocated by bill inventory ratio + direct Purchases; 11900 + 11901 Boise) | `src/inventorySpend.ts` | Done, tested |
| Cash P&L (bank-landed income, tax contra-revenue, rebate/reimbursement offsets, direct costs) | `src/pnl.ts` | Done, tested |
| Accounting-basis COGS from physical inventory counts (`inventory_counts` table + entry UI on /pnl) | `src/index.ts` `/api/pnl-statement` | Done; counts seeded for 2025-12-31 ($2,667,236.20) and 2026-06-30 ($3,264,289.74) |
| Raw-transaction Postgres mirror + incremental sync + local-first compute | `src/sync.ts` | Done; boot sync + 12h interval; full-year recompute in seconds |
| Direct-method bank reconciliation | `src/bankflow.ts`, `/bank` page | Done; ties exactly for H1 (see h1-2026-verified-numbers) |
| Balance-sheet cash-flow view | `src/cashflow.ts`, `/cashflow` page | Done |
| Monthly result caching (closed months forever, current month 1h TTL, `refresh=1` bypass, in-flight dedupe) | `src/index.ts`, `monthly_cache` table | Done |
| Report-hub UI, Apple-like theme, day-level date ranges shared across pages, stale-response guard | `public/` | Done, deployed 2026-07-15 |
| Email magic-link auth (Resend) | `src/auth.ts` | Code done and tested, **NOT ACTIVE** — `RESEND_API_KEY` unset, so the dashboard is open to anyone with the URL. This is the current goal. |
| QBO throttling (150ms gap + backoff), error sanitization, no `express.static` | `src/qbo.ts`, `src/index.ts` | Done |

## Environment (Railway) — actual values in use
- `QBO_INVENTORY_ACCOUNTS=11900,11901` · retail income = all Income/Other-Income accounts · sales tax account `21900` · `QBO_DIRECT_COST_ACCOUNTS=51300,50600,50200` (freight, customer repairs, materials)
- `TOKEN_ENCRYPTION_KEY` lives only in Railway env — never materialize it locally
- `RESEND_API_KEY` **unset**; auth allowlist defaults to `chrism@pictureline.com`

## Validated output (H1 2026, 2026-01-01 → 2026-06-30)
Net revenue **$12,605,769.65**, accounting-basis COGS **$10,594,351.46**, GP **$2,011,418.19** (16.0%), NOI **$713,506.82**. Full breakdown and how each line was tied out: [../concepts/h1-2026-verified-numbers.md](../concepts/h1-2026-verified-numbers.md).

## Known discrepancies memory vs repo
None found on 2026-07-15 cross-check — git log matches the narrative in `entities/decisions.md`.
