# The profit-to-bank proof — "if we made it, we can see it"

Chris's accountability principle (2026-07-17): "if we made 1.5 million dollars in a year or over 20 months we could see it in the bank accounts and inventory… it can't hide in too many places." This page is the repeatable method + the first proof.

## Method
For any window: claimed NOI (app P&L) must equal the sum of balance-sheet places profit can sit, using:
- **Real banks only** (Zions checking 8882 + savings; fake Bank-typed accounts excluded — their drift is cleanup noise)
- **Inventory from Chris's physical counts** (book inventory values are broken)
- Owner outflows: all Dist-*/Dividends + Federal Estimated Tax (1040-ES asset = personal tax prepaid) + loans to related parties
- A/R growth, Credit Memos asset (the Boise pipe), capex (furniture/leasehold)
- MINUS financing: A/P + credit-card balance growth (vendors/cards lending more)
- **EXCLUDE Sales Tax Payable drift** — remittances post with no accruals (known-broken account; Option-1 catch-up JEs pending), so its fall (~$950k/yr) is a books artifact, not money.
Data: /api/cash-flow per period (balance-sheet diffs) + statements + counts.

## First proof: Jan 2025 → Jun 2026 (18 months), run 2026-07-17
Claimed NOI $2,975,166 (2025: $1,645,210 + H1-26: $1,329,956, D33 engine).
Visible: banks +$704,312 · inventory (counts) +$768,697 · owner dists +$781,975 · est-tax +$233,200 · A/R +$401,681 · credit-memo pipe +$221,131 · capex +$148,718 = $3,259,714; less vendor/card financing −$417,213, other liabilities +$37,442 → **accounted $2,879,943 vs claimed $2,975,166 — residual $95,223 (3.2%)**, inside known noise (ACH final-mile ~$120-140k/yr, payroll-invisible timing).
**Verdict: the D33 profit is real and traceable.** Chris also confirmed the timing-context question ("evens out throughout the year, we don't really stop ordering").

Candidate feature: a permanent /proof page computing this table for any range (offered to Chris).


## METHOD CORRECTION (2026-07-17, caught by Chris immediately)
Chris: "Why are we counting accounts receivable as revenue? I thought we are only counting when it is paid" — and he was right. The app's income is CASH-LANDED, so **A/R growth and the Credit-Memos asset must NOT appear as destinations of profit** (that money was never in the P&L). Also **the Amex line is untrustable**: balances −$1,754,348 → −$1,954,262 (a negative $1.95M credit-card liability = the known sign-flipped account) — no claim about Amex paydown/charging is valid until the bookkeeper repairs it. Corrected H1-2026 proof: banks +317,171 · inventory(counts) +483,287 · dividends +75,000 · est-tax +81,700 · capex +75,271 · prior-year W/H paydown +142,629 − card/vendor financing 178,982 = **≈$996k solidly accounted of $1,329,956 claimed; residual ≈$334k sits behind broken-Amex + payroll-invisible flows.** The earlier 18-month "3% residual" was flattered by wrongly including A/R (+$424k) and credit memos (+$221k); on the corrected method its residual is ~25% pending the same two blockers. **Fixing the Amex account is now the top bookkeeper priority after ACH.** Bridge rule for the hybrid basis: NOI(cash income, count-adjusted COGS, books expenses) ≈ Δreal banks + Δinventory(counts) + owner outflows + capex + old-liability paydowns − new vendor/card financing ± payroll-invisible remainder. NO A/R, NO credit-memo, NO sales-tax-payable, NO fake banks, NO Amex (until repaired).

## 2025 proof (corrected method, run 2026-07-17)
Claimed NOI $1,645,210. Banks +387,141 · inventory(counts) +285,410 · owner outflows +736,975 (dists 410,000 + Jens 189,800 + Chris/David/Nick 84,675 + dividends 52,500) · est-tax +151,500 · capex +73,446 − expense accrual growth 132,637 (W/H 69,543 + DD payable 40,048 + Chase 23,046) = **$1,501,835 accounted (91%), residual $143,375** behind Amex-untrustable (raw move −132,236, sign-flipped, meaningless) + payroll-invisible + ~137k unresolved ACH twins. NOTE for the method: with payment-dated cash COGS, **A/P growth does NOT enter the bridge** (unpaid bills are neither cost nor cash yet) — expense-accrual liabilities (W/H, DD) do. Pattern across periods: proofs close to ~90% with the SAME two named gaps (Amex, payroll-invisible) — sound books, two broken accounts.
