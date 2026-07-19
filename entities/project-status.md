# Project status — WHAT IS DONE

**As of: 2026-07-19, session end** (build passes, 48/48 tests; PR #2 open and carries the whole sprint). Reports live: P&L (inline drill drawers, year/period picker, vs-year compare, cold load 1.6s), /money (waterfall profit proof; **2025 traces 99.0%**), /bank, /cashflow, /checks (**10/10**), /cleanup (admin-gated).

## The 2026-07-19 card-repair + speed session (log addenda 16–20)
- **Card-register diagnosis corrected then FIXED for 2025–26.** Twin-matcher falsified the "payments recorded twice for five years" theory (Add.16): the −$2.0M Platinum register is mostly MISSING CHARGES (pre-Jul-2022 history ~$517k; 2024 feed collapse ~$500k — QBO never backfills feed gaps >90 days). 2025–26 data is COMPLETE; its 9 double/cross-coded payments ($358,305.61) were recoded to the Credit Cards wash account by the engine (tasks `card-payments-2025-26-amex` 8 rows + `-purple` 1 row, Chris-approved, verified penny-exact: register 63 → −1,750,901.10, 100 → +387,826.39). Before-images in reclassify_log.
- **Money-map now measures 2025+ card movement from transactions** (registerMovement helper) instead of dropping broken registers: residuals now 2023 $250,819 · 2024 $196,204 · **2025 $16,909 (DONE)** · H1-2026 $206,813 (≈ AXP payments hiding in expense accounts ~$90–120k + payroll cash-later wedge).
- **CreditCardPayment entity mirrored end-to-end** (raw query path in qbo.ts — node-quickbooks predates it); new read-only diagnostics: /api/card-dupe-match, /api/txn-raw, /api/register-history?list=1.
- **P&L cold load 33.3s → 1.6s** (batch getBills prefetch in inventorySpend bucket 1) + **post-sync cache pre-warming** (current YTD + last 3 years) so Chris always lands warm (~0.3s). NOIs verified unchanged; checks 10/10 (new #10 payment-line-identity).
- **Waiting on Chris (his todo list, roadmap item 3):** ① Dec-2024 Amex statement balances (Platinum predicted **$364,944.19**; Purple's number sizes the hidden-payment hunt) ② one truing JE with the accountant (register → real feed balance ~−$70,706) ③ bookkeeper: bounced 07/02 autopay $76,640.30 booked twice (CCPs 60291/60392), return never booked.


## The 2026-07-16/17 cleanup sprint (see log/ + concepts/ach-clearing-account-h2-2025.md)
- **Password auth ACTIVE** since 2026-07-15 (Chris set `ADMIN_EMAIL`/`ADMIN_INITIAL_PASSWORD` and signed in; agent service account `claude-agent@pictureline.com` exists, **currently admin per D28** — revoke with `AGENT_IS_ADMIN=false` + redeploy when cleanup ends).
- **Reclassify write engine live** (`/cleanup`, D27/D28): tasks `ach-belt` (425 rows — done: engine + Chris's hand conveyor), `ach-stragglers` (23 rows — 21 reclassified $580,540.37, 1 hand-done, 1 correctly refused), `tax-pulls-2023` (16 rows — all already hand-fixed by Chris, verified + retired), `tax-pulls-2024` (8 rows, $298,197.82 — **run by the agent 2026-07-17, all reclassified**).
- **ACH balance: −$13,062,007.66 → −$159,135.97.** Remaining: 14 unmatched payments (~$259k), 8 quarantined rows (~$292k), overpayment residue (~+$102k) → one 68000 write-off entry.
- **Exclusion (D26) REMOVED — done.** Chris deleted the variable in the Railway UI 2026-07-17; every payment counts once; /checks re-ran 9/9 on the final engine.
- **D33 recompute (corrected cash engine — credits excluded, payment-dated, physical counts):** 2023 NOI **$1,540,848** (GP 19.72%) · 2024 **$1,019,620** (16.78%) · 2025 **$1,645,210** (21.81%) · H1-2026 **$1,329,956** (20.95%). Phantom credit-cost removed per year: $1.25M/$1.52M/$1.24M/$0.64M. Fit vs Chris's calibration: 2023 ≈$1.8M ✓ close, 2024 ≈$1M ✓ dead-on, 2025 "smaller than 2023" ✓ (1.65M; accountant's SLC-only-with-write-downs 2025 shows $1.08M — write-downs and scope explain part, not fully reconciled). Remaining small biases: un-reclassified ACH twins still in direct buys (~$121k 2024 / ~$137k 2025, shrinks as the final-mile list closes). H1-2026 jumped from ~$0.6M — earlier "validated" H1 carried the credit bug inside the payroll remainder of the direct-method tie-out; /checks needs a re-run.
- *(superseded by D33)* D31 recompute (inventory-received basis, 2026-07-17): 2023 NOI **+$783,898** (GP 16.43%) · 2024 **+$510,085** (14.55%) · 2025 **−$245,340** (12.82%) · H1-2026 **+$492,762** (14.27%). Purchases received (bills+direct−credits): $18.80M/$19.54M/$18.35M. Chris's calibration: 2024+2025 ≈ $1M combined (currently $264,744). Two live levers: the 12/31/2025 count Boise question (~$370k swings 2025 vs 2026) and the ACH final-mile twins still inflating 'direct buys' (~$121k in 2024, ~$137k in 2025, plus txn 31282's $144k real-or-duplicate question in 2024).
- *(superseded)* Exclusion removed by Chris 2026-07-17 (D30 executed). First cash-basis clean recompute (accounting basis, year-end physical counts Chris entered): **2023 NOI $292,541** (GP 14.3%) · **2024 NOI −$499,764** (GP 10.12%) · **2025 NOI +$409,003** (GP 15.94%). Cash-basis: $612,685 / −$657,884 / $123,593. KNOWN BIASES still in 2024–25: (a) unfinished punch-list leftovers now DOUBLE-count (~$292k quarantined wires + up to ~$259k unfound twins of the 14 unmatched payments); (b) costs now sit at PAYMENT dates — the old wire-dated view flattered 2025 (H2-2025 payments $4.73M vs wires ~$3.5M, remainder cleared 2026) and the old '2024+2025 sum ≈ $1.74M' estimate was wire-dated, so the drop is a correction, not a regression. Expect both years to rise as the punch list closes. 2023 still rises with the remaining manual 66000 items (IRS pair, JENS $67k, teller $24.5k, direct-deposit deletes + $48,990.88 transfer, SUI, 401k).
- Inventory counts edited by Chris 2026-07-16: 2025-12-31 → $2,783,704.06, 2026-06-30 → $3,266,991.24 (H1-2026 NOI accordingly $599,740.46, was $713,506.82). Resolved 2026-07-17: Chris confirms his counts are accurate company-wide; the accountant's differing figures are SLC-only + write-downs.

---
*The sections below describe the 2026-07-15 state and remain accurate except where superseded above.*

## Deployed and in use
- Live at `https://web-production-8c8c0.up.railway.app` (Railway project `456f070c-f728-434f-9e8e-56923d776bc6`, Nixpacks, Postgres plugin, healthcheck `/health`).
- Connected via OAuth to Pictureline's **production** QuickBooks Online company (read-only). Tokens AES-256-GCM-encrypted in Postgres; auto-refresh 5 min before expiry.
- PR #1 **merged to `main`** 2026-07-15 (merge commit `9db9733`, 36 commits) at Chris's request. The app still deploys via `railway up` from a working tree; future code work branches from `main`.

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
| Password auth (goal 01) | `src/auth.ts`, `/login` `/password` `/users` | Built & deployed: scrypt hashing, admin user CRUD, forced first-login password change, per-request user recheck. **NOT ACTIVE** until Chris sets `ADMIN_EMAIL`+`ADMIN_INITIAL_PASSWORD` (goal 01 has the commands) — dashboard open until then. |
| P&L drill-down (goal 02) | `src/pnlDetail.ts`, `/api/pnl-detail`, `/api/expense-detail`, `/pnl` UI | Done, verified live: every H1 drill-down sums exactly to its statement line; QBO deep links; CSV export; payroll remainder labeled explicitly. |
| Checks report (goal 03) | `/api/checks`, `/checks` page + hub tile | Done, verified live: 9/9 checks PASS for H1 2026 (tax to the penny; direct method ties to +$398,871.31 bank change). |
| QBO throttling (150ms gap + backoff), error sanitization, no `express.static` | `src/qbo.ts`, `src/index.ts` | Done |

## Environment (Railway) — actual values in use
- `QBO_INVENTORY_ACCOUNTS=11900,11901` · retail income = all Income/Other-Income accounts · sales tax account `21900` · `QBO_DIRECT_COST_ACCOUNTS=51300,50600,50200` (freight, customer repairs, materials)
- `TOKEN_ENCRYPTION_KEY` lives only in Railway env — never materialize it locally
- `ADMIN_EMAIL` defaults to `chrism@pictureline.com`; `ADMIN_INITIAL_PASSWORD` **unset** (this is what keeps the gate off). `RESEND_API_KEY` removed from the code entirely (D24).
- Inventory counts in the system: 2025-12-31 ($2,667,236.20), 2026-06-30 ($3,264,289.74), and 2026-07-15 ($3,370,299.92 — entered by Chris himself, the monthly habit is live)

## Validated output (H1 2026) — SUPERSEDED by D33
The 2026-07-15 validation (net revenue $12,605,769.65, NOI $713,506.82, GP 16.0%) carried the credit bug (D33) inside the tie-out's payroll remainder. Current H1-2026: **NOI $1,329,956, GP 20.95%** (see the sprint section above). The old line-by-line method in [../concepts/h1-2026-verified-numbers.md](../concepts/h1-2026-verified-numbers.md) remains a good template; its numbers do not.

## Known discrepancies memory vs repo
None found on 2026-07-15 cross-check — git log matches the narrative in `entities/decisions.md`.
