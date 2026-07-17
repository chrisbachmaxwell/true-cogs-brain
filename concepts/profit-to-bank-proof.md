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
