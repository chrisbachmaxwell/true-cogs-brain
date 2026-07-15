# 2026-07-15 — Brain created (retroactive diary of the build session)

This is the session diary that was never written: the whole project was built in one continuous session, 2026-07-11 → 2026-07-15, by a single agent working with Chris. Summarized from that session's context while it still existed.

## What was built (chronological)
1. **Initial tracker** (`89806ea`): Express/TS app, QBO OAuth (encrypted tokens in Postgres), two-bucket cash COGS for #11900, dashboard, deployed to Railway against the PRODUCTION QuickBooks company (Intuit wouldn't issue Chris a sandbox; app is read-only so we skipped it). Walked Chris click-by-click through Intuit's production-app questionnaire.
2. **Hardening** (`f06e810`, `addb28a`): fixed vendor-discount over-attribution (positive-lines denominator), added QBO throttling after 429s, sanitized errors after an access token leaked into the dashboard banner, built magic-link auth (dormant until `RESEND_API_KEY`).
3. **Both locations** (`1355c93`, `9e3f4f9`): #11901 Boise added, per-account toggle.
4. **Cash P&L** (`4381e2f` → `0bc9e76`): income definition iterated five times WITH Chris (all-income → tax-inclusive → tax remitted as contra-revenue once → full-value invoice payments → bank-landed only after finding $569,397.82 settling into the Credit Memos asset). Each step is a dated decision in `entities/decisions.md` (D6–D8).
5. **Reconciliation suite** (`230abd1`, `4231978`, `b89e6e6`, `d1b74eb`, `288aa0e`): balance-sheet cash-flow view, direct-method bank reconciliation (discovered payroll/tax-center are API-invisible — $727k H1 gap, explicit line), deposit-lines and payments audits.
6. **Offsets & direct costs** (`87970a5`, `ad7f575`): rebates reduce COGS, co-op reduces expenses, freight/repairs/materials cash-traced into COGS.
7. **Sync layer** (`964c53b`): Postgres transaction mirror, incremental sync, local-first compute — full-year recomputes in seconds.
8. **UI redesign** (`bd35f19`): report hub + four drill-downs, day-level ranges, Apple-like theme.
9. **Accounting-basis COGS** (`f6bf371`): physical inventory counts table + UI; matching-principle COGS; H1 NOI $713,506.82 (vs cash-basis $116,453.28).
10. **Final polish** (`7ddca5d`, deployed & verified today): removed the cash-basis NOI memo (Chris: one statement, accounting principles) and fixed the stale-response race his screenshot exposed (staleGuard in all five pages).

## Validation state
H1 2026 fully tied out — see `concepts/h1-2026-verified-numbers.md`. Build + 28 unit tests pass at `7ddca5d`.

## Business findings delivered to Chris along the way
Boise settlement structure (cash $802k + credit memos ~$405k of $1,207k booked), monthly JEs are intercompany bookkeeping, ledger-hygiene punch list (`concepts/ledger-hygiene-items.md`), the $30k Shareholder-Distributions deposit flag, the unresolved "$997k QuickBooks money-in/out" widget mystery (not a bank fact).

## Open loops at brain creation
- PR #1 open (draft), watched with hourly check-ins by the build session.
- Goal 01 (auth gate) BLOCKED on Chris creating a Resend key.
- Railway token rotation owed (security-notes).
- Parked by Chris: bank-ledger CSV reconciliation, Boise credit-memo audit.

## Brain created today
Full structure per Chris's brain spec: INDEX, rules, status, roadmap, 23 decisions, 17 concept pages (dead ends first-class), goal loop + goal 01, three role playbooks, this log, raw/. Code repo wired via CLAUDE.md/AGENTS.md pointing here.
