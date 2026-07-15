# Goal 04 — One set of formulas everywhere (dashboard = P&L)

**Status:** ACTIVE — reported by Chris 2026-07-15, fix implemented same day; awaiting his confirmation.
**Origin:** Chris: "the ytd p&L says we've made 571 and the dashboard says we lost money. I want it to be the same formulas."

## Root cause (diagnosed, not speculative)
The hub's KPI tiles read the CASH-basis fields of `/api/pnl-statement` (`cogs.total`, `grossProfit`, `noi`) while the P&L page shows the ACCOUNTING-basis block (`adjusted.*`) whenever inventory counts cover the range. YTD 2026 inventory grew ~$703k (counts 2025-12-31 $2,667,236.20 → 2026-07-15 $3,370,299.92), so cash NOI was negative while accounting NOI was ≈ +$571k. Both numbers were "correct" — they were different formulas, which is exactly what Chris doesn't want (see decisions D13, D25: one statement, accounting principles).

## The rule this goal encodes (for all future UI work)
**Any surface that displays COGS, gross profit, margin, or NOI must use `adjusted.*` when present, falling back to cash basis only with an explicit label.** The P&L page, hub KPIs, and any future report or export all follow the same selector. Never present cash-basis profit as THE number when counts exist.

## Done when
- [ ] With the same date range, the hub KPI tiles and the P&L statement show identical COGS, gross profit, margin, and NOI.
- [ ] The COGS tile labels its basis ("Accounting basis (counts X → Y)" or "Cash basis — no inventory counts for this range").
- [ ] YTD range (Jan 1 → today) shows a positive NOI on the dashboard matching the P&L (~$571k as of 2026-07-15).
- [ ] Chris confirms the numbers agree on his screen.

## Iteration log
- 2026-07-15 (worker): fixed `public/index.html` — KPI tiles now derive from `adjusted.*` when present, identical to pnl.html's selector, with the basis labeled on the COGS tile. Deployed with the multi-level drill-down batch.
