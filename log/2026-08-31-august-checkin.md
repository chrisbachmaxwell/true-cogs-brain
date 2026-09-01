# 2026-08-31 — August check-in (prediction scorecard)

Chris: "It's August 31." Re-auth via approved service-account flow; sync fresh; Chris has kept a
~weekly count habit (7/28 $3,425,686 · 7/31 $3,387,856 · 8/8 $3,300,910 · 8/19 $3,287,506 — no 8/31 yet).

## Numbers (book basis, cash-verified)
- **YTD (thru 8/31): revenue $16,184,215 · NOI $1,537,830 · GP 20.45%** (margin recovered from July's 18.1%)
- **Q3-to-date: NOI +$207,874** (was −$322,868 at 7/21 → July+Aug swung ~+$530k — the predicted
  prepaid-goods rebound is happening)
- **August alone: revenue $1,781,828 · NOI $133,445 · GP 20.71%** (understated: end count 8/19, 12-day gap)
- Balance sheet 8/31: checking $961,769 · savings $353,020 · **TOTAL BANK $1,314,788** (+$157k since 7/21
  ≈ $115k/mo — bank resumed climbing as predicted) · **A/P $117,349** (rebuilt from −$72k; bills being
  entered again)
- Economic YTD ≈ book + net A/P paydown ($682,747→$117,349 = $565,398) ≈ **~$2.10M** — tracking the
  upper-middle of the July forecast band (base $2.1-2.4M accrual; Chris's bull case ~$3.2M).

## Honest watch-items
- **August revenue ($1.78M) ≈ flat vs Aug-2025 (~$1.80M/mo)** while H1 ran +20% YoY — the "Aug–Dec is our
  best" seasonality hasn't shown up yet; Sept–Dec must deliver for the bull case.
- **Magic-link email STILL failing 535 bad-username/password** — Chris never fixed the SendGrid vars
  (SMTP_USER must be literal "apikey" / full SG. key / Mail Send permission). Password fallback fine.
- Suggest an 8/31 month-end count to tighten August; no count since 8/19.

## Vendor payment rhythm (new /api/vendor-payments, per-vendor per-month bill cash)
Chris asked if the A/P up-down is random / whether non-Canon brands get paid irregularly. Finding:
**payments are STEADY for every brand every month** (Canon 110-573k, ASI 128-330k, Fujifilm 68-157k,
Leica/Aputure/Ingram/Sigma 20-70k — no lumpy sweeps). The A/P swings come from the BILLING side:
holiday deliveries pile bills Nov-Dec (year-end A/P peak), January's delivery lull lets steady payments
crash it (683k→139k — not a special event), spring restock rebuilds (→895k May), July = summer delivery
lull + the ~418k "Vendor"-placeholder catch-up batch → through zero. July sales were UP 18% YoY ($1.86M)
and inventory flat — payoff was discretionary from peak cash ($1.37M 6/30), not distress. Predicts:
A/P rebuilds Sept-Nov with holiday buy, collapses again Jan-2027. Refinement stands: ride full terms on
0%-discount brands during the fall buildup (~$300k stays in bank).
