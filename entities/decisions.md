# Decisions — what we chose and WHY

Every entry: claim → reasoning → source. All dates 2026-07 (the project was built 2026-07-11 → 2026-07-15 in one continuous session). "Chris" = Chris Maxwell, owner, non-technical.

## Methodology (the heart of the project)

**D1 — The question the app answers is cash-out, not accrual.** "How much actual cash and credit-card money left the business for Material Inventory purchases that month?" Chris distrusts journal-entry-driven books ("I want it to be actual cash backed transactions for everything. Not journal entries."). Source: initial spec + conversation. Commit `89806ea`.

**D2 — Read-only QuickBooks app, production keys, no sandbox.** Intuit wouldn't let Chris create a sandbox company; since the app never writes, we skipped sandbox entirely and went straight to production credentials. Source: conversation ("our app isn't writing data to quickbooks only reading it correct? if so we can skip the sandbox").

**D3 — Two-bucket cash COGS.** Bucket 1: BillPayments, allocated to inventory by the paid bill's inventory share of its positive charge lines. Bucket 2: direct Purchases coded to inventory accounts. Vendor credits are already net inside payment lines, so they are never subtracted again (double-subtraction bug class). Source: initial spec; refined in `f06e810`.

**D4 — Allocation denominator = positive lines only.** Bills with negative vendor-discount lines made ratio > 1 and over-attributed inventory. Chris confirmed the business reality: "we have vendor discounts so the bill is larger than what is actually paid… split category." Fix: `positiveLineTotal`. Source: `f06e810`. Details: [../concepts/vendor-discount-ratio-fix.md](../concepts/vendor-discount-ratio-fix.md).

**D5 — Track BOTH inventory accounts: #11900 Material Inventory and #11901 Boise Inventory,** with a per-account toggle in the UI. Chris caught the omission: "what about 11901 material inventory Boise". Once income included intercompany money (D7), COGS had to include Boise purchases for consistency — Chris stated this linkage himself. Source: conversation; commits `1355c93`, `9e3f4f9`.

**D6 — Income evolved through five refinements, each driven by Chris.** Final rule: income = customer cash that **physically landed in a Bank-type account**, counted **tax-inclusive at full value**. The journey: retail-account-only → all income accounts ("Basically all income that flows into the bank account money received should be seen as a revenue") → tax-inclusive because Lightspeed POS syncs tax-inclusive daily totals into 40100 → full-value invoice payments ("we've already accounted for the expenses of those items. So not including it doesn't make sense.") → bank-landed-only after the $569k credit-memo discovery. Source: conversation; commits `90d863f`, `28435f0`, `2502f71`, `44400fa`, `0bc9e76`. Details: [../concepts/bank-landed-income-rule.md](../concepts/bank-landed-income-rule.md).

**D7 — Intercompany (Boise) settlements count as revenue.** Chris: "Inter company revenue should be included." Consequence: Boise inventory purchases count in COGS (D5). Source: conversation.

**D8 — Sales tax is deducted from revenue exactly once, as contra-revenue ("Less: sales tax remitted"), traced to actual remittance transactions.** Chris pushed back hard on tax-as-expense: "it shouldn't be counted in our revenue… It should be in a holding account and our profit should have been less." But because Lightspeed syncs tax-INCLUSIVE income, the money IS in revenue; deducting remittances once reconciles both his intuition and the books. Validated to the penny against ledger movement ($632,030.98 H1). Source: conversation; commit `2502f71`. Details: [../concepts/sales-tax-contra-revenue.md](../concepts/sales-tax-contra-revenue.md).

**D9 — Deposited vendor rebates reduce COGS; ad co-op reimbursements reduce expenses.** Chris: "The money from co op reimbursements should lower expenses same with vendor rebates." Source: conversation; commit `87970a5`.

**D10 — Freight (51300), customer repairs (50600), and materials (50200) are cash-traced and added to COGS as "direct costs".** Chris: "please use the freight cost and repairs/materials to increase cogs." The reference P&L PDF he supplied showed freight inside COGS, not expenses. Source: conversation; commit `ad7f575`.

**D11 — The P&L follows accounting principles: COGS = beginning inventory + purchases − ending inventory, using PHYSICAL COUNTS Chris enters monthly.** He supplies the counts ("yes I want that. I can add those monthly") because Boise inventory value isn't derivable from QBO cash data. Chris confirmed accounting basis is "the right" number. Source: conversation; commit `f6bf371`. Details: [../concepts/accounting-vs-cash-cogs.md](../concepts/accounting-vs-cash-cogs.md).

**D12 — Operating expenses come from the books' accrual P&L report, not cash-traced.** Chris, on the P&L PDF he shared: "most of the expenses in here I think are accurate so you can use those." Cash-tracing every expense would also hit the payroll-invisibility wall (see qbo-api-invisible-transactions). Source: conversation.

**D13 — The P&L report shows ONE statement — accounting basis only.** The cash-basis NOI memo line was removed at Chris's request: "I just want to look at one report that has the P&L according to accounting principles." Cash-basis appears only as a clearly labeled fallback when a range has no surrounding inventory counts. Source: conversation 2026-07-15; commit `7ddca5d`.

## Architecture

**D14 — Raw-transaction mirror in Postgres, local-first compute.** Chris: "shouldn't we be storing the data instead of constantly making api calls… yes please build the sync." Ten entity types mirrored to `qbo_txns`; incremental sync via `MetaData.LastUpdatedTime`; compute reads locally when mirror <26h fresh; QBO report endpoints still hit the API. Full-year recompute went from ~30 rate-limited minutes to seconds. Source: conversation; commit `964c53b`.

**D15 — Monthly result caching in Postgres:** closed months cached forever, current month 1h TTL, `?refresh=1` bypass, view-keyed (`pnl:`/`stmt:`/`cf:`/`bf:` prefixes) and range-keyed, plus server-side in-flight dedupe so concurrent identical requests share one computation (added after the deploy-race incident — see concepts). Source: commits `230abd1` onward.

**D16 — QBO throttle: shared queue with 150ms min-gap + exponential backoff on 429** (production limit ~500 req/min). Added after first trend load rate-limited. Source: `f06e810`.

**D17 — Email magic-link auth via Resend, enforced ONLY when `RESEND_API_KEY` is set.** Single-use 15-min tokens (SHA-256-hashed at rest), 30-day HMAC-signed cookies (timing-safe compare, allowlist recheck), no user enumeration, rate limiting. Allowlist defaults to `chrism@pictureline.com`. Conditional enforcement chosen so the app stayed usable while Chris hadn't yet created a Resend account — accepted risk, repeatedly flagged. Source: `addb28a`; roadmap item 1.

**D18 — No `express.static`; every page is an explicit authenticated `sendFile` route.** `express.static` would serve pages around the auth gate. Only `/theme.css`, `/app.js`, `/login`, `/connect`, `/callback`, `/health` are open. Source: conversation (auth-bypass review).

**D19 — QBO OAuth tokens encrypted AES-256-GCM** with a key derived (SHA-256) from `TOKEN_ENCRYPTION_KEY`, stored in Postgres; refresh tokens rotate. Source: initial build `89806ea`.

**D20 — Errors shown to the user are sanitized** (`qboError`: status + QBO Fault only). A raw axios stringify once leaked the Authorization header into the dashboard banner. Source: `f06e810`. Details: [../concepts/token-leak-error-sanitization.md](../concepts/token-leak-error-sanitization.md).

**D21 — `main` was created as an empty commit and merged with `--allow-unrelated-histories`** because the permission classifier denies force-pushes; this gave PR #1 a base without history rewrite. Source: commits `04445c9`, `86f0f49`.

**D22 — Frontend is dependency-free vanilla JS** (except Chart.js from CDN) with a shared `theme.css` (Apple-like: frosted topbar, SF stack, soft cards, dark mode) and `app.js` (shared `Range` state URL⇄localStorage, `staleGuard`). Chris asked for "a modern apple like aesthetic" and a hub-of-icons dashboard with drill-down reports and day-level date pickers. Source: conversation; commit `bd35f19`.

**D23 — Direct-method bank reconciliation as its own report.** Chris wanted proof: "I guess I'm trying to find out why all the net profit didn't go straight to the bank account… we should be able to reconcile that number." Built `/api/bank-flow`: transaction-by-transaction money in − categorized money out; the remainder is payroll/tax-center (API-invisible), shown explicitly rather than hidden. Source: conversation; commit `4231978`.

**D24 — Password-based user management replaces email magic-link auth.** Chris (2026-07-15): "instead of doing a login with email, lets use a user management with passwords." Removes the Resend/email dependency that had blocked goal 01 (the magic-link gate was never activated). Design: scrypt-hashed passwords, env-seeded first admin, admin UI for add/reset/remove users; HMAC session cookies retained from the old flow. Source: conversation 2026-07-15; goal 01 rewritten.

**D25 — P&L drill-down with self-verifying detail views; reconciliation checks page proposed.** Chris (2026-07-15): clicking any statement amount must show the transactions/accounts composing it ("This drill down is important to me so I can see where our numbers are coming from"), including a two-level expenses breakdown. Agreed enhancements: every detail view carries a sum-check line proving it ties to the statement number; transaction rows deep-link to the source document in QuickBooks; CSV export per table. A "Checks" page automating the H1 tie-out invariants was proposed and specced (goal 03, awaiting Chris's go). Source: conversation 2026-07-15; goals 02 and 03.

## Chris's preferences and constraints (observed)
- **Non-technical.** Give exact commands and expected output; never assume CLI fluency. He interacts via the web UI and screenshots.
- Wants **one number he can trust**, reconciled to the bank — distrusts figures he can't tie out, and will personally audit them (he caught several methodology errors).
- Dislikes dual/parallel presentations (cash vs accrual side-by-side) — one statement, accounting principles (D13).
- Wants plain-language explanations of accounting concepts; explain WHERE money went, not just totals.
- Provides business context willingly and corrects the model quickly — when a number looks wrong to him, he is usually right about the business and the methodology needs to move toward his mental model, not vice versa.
- Prefers stored data over repeated API calls; speed matters (D14).
- Will do monthly manual work (inventory counts) if it buys accuracy (D11).
- Aesthetic: Apple-like, modern, not "plain".
