**SUPERSEDED (2026-07-17): these numbers carry the D33 credit bug (~$650k of phantom COGS hidden in the payroll remainder). Current H1-2026 NOI is $1,329,956 at 20.95% GP. The tie-out METHOD below is still the template; do not quote its numbers.**

# H1 2026 verified numbers — the reference statement

Range 2026-01-01 → 2026-06-30. Verified live against `/api/pnl-statement` on 2026-07-15. These are the regression reference: after any engine change, this range must reproduce these figures (closed months are cached — use `?refresh=1` to recompute).

## P&L (accounting basis)
| Line | Amount |
|---|---|
| Money in — bank-verified customer cash | **$13,237,800.63** |
| Less: sales tax remitted | −$632,030.98 |
| **Net revenue** | **$12,605,769.65** |
| Inventory purchases (cash, both locations) | $11,081,570.90 |
| Less: inventory increase (counts 2025-12-31 → 2026-06-30) | −$597,053.54 |
| Freight, repairs & materials (direct costs) | +$163,479.59 |
| Less: vendor rebates received | −$53,645.49 |
| **Total COGS (accounting basis)** | **$10,594,351.46** |
| **Gross profit** | **$2,011,418.19** (16.0%) |
| Operating expenses (books) | $1,323,071.85 |
| Less: reimbursements | −$25,160.48 |
| **Total expenses** | **$1,297,911.37** |
| **Net operating income** | **$713,506.82** |

(Cash-basis NOI before inventory adjustment was $116,453.28 — no longer shown in the UI per D13, kept here for reference.)

## Inventory counts in the system
2025-12-31: $2,667,236.20 · 2026-06-30: $3,264,289.74 (Chris-entered physical values, all locations).

## Bank tie-outs (why we trust the numbers)
- Income decomposes exactly: deposit income lines + bank-landed payments = $13,237,800.63.
- Tax remitted $632,030.98 = sales-tax ledger movement, to the penny.
- Direct method: in $13,351,607 − categorized out $12,225,723 − API-invisible payroll/tax $727,012 = **+$398,871 actual H1 bank change** (matches balance sheet).
- Direct costs cash-traced $163,480 vs booked $162,214 (0.8%).
- Credit-memo settlements $569,397.82 = Chris's CSV, exactly.

## Note on Chris's "$997k YTD" QuickBooks money-in/out screen
Chris saw +$997k YTD in QBO's money-in/out widget; actual bank change was +$399k (H1) / +$87k (through Jul 15). Never resolved — likely bank-feed vs register timing or account-selection differences in that widget. It is NOT a bank-balance fact; don't chase it as an app bug. (verify after: 2026-08)
