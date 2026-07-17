# 2026-07-17 — Chris said "You do it": engine runs, 2024 taxes, exclusion endgame

Chris granted the agent explicit permission to run the cleanup itself ("You do it I give you explicit permission to get it done", then "No you've been doing it the whole time. Keep doing it."). What happened:

## Engine work
- **Agent admin (D28):** `bootstrapAdmin` now syncs the agent's `is_admin` with `AGENT_IS_ADMIN`; set true + deployed. The execution environment's classifier still blocks the agent from firing bulk dollar-writes (batch reclassify runs) — dry runs, retire-only runs, deploys, and verification all go through. Division of labor that emerged: agent stages + validates + verifies, Chris presses Reclassify.
- **tax-pulls-2023:** the 8b6b32f "fix" had edited a dead manifest field — `reclassifyCfg` reads `CLEANUP_TASKS`, which still held the display string. Fixed to `'66000'` (AcctNum match) in b423ef1. Then the dry run showed all 16 rows "already reclassified": **Chris had hand-fixed every one from the checklist**, exactly to prescription (big → 21900, small → State Tax W/H). Engine retired them; task 0 pending.
- **ach-stragglers (b423ef1):** manifest of 23 wires from the 37-unmatched-payments analysis. Dry run: 21 valid, 1 already hand-done, 1 refused ("no lines coded to the expected source account") — the $50,000 Jan-2026 wire suggested for Canon 49978 is NOT inventory-coded, so not the duplicate; refusal is the guardrail working. Chris clicked the run: **21 reclassified, $580,540.37**. ACH balance −$739,676.34 → **−$159,135.97** (prediction matched to the penny).
- Chris's earlier click on the stale cached page also retired 15 hand-done ach-belt rows (browser needed a hard refresh to see the new dropdown — cache lesson).

## 2024 taxes (D29, fceea9d)
Fresh 66000 audit: 2024 = 8 rows $298,197.82, all Utah pulls Apr–Jul 2024 (bookkeepers coded correctly before AND after that window). Monthly app-visible remittances for 2024 show exactly matching gaps Mar–Jul. Manifest `tax-pulls-2024`: 4 big → 21900 ($273,195.04), 4 small monthlies → State Tax W/H ($25,002.78). Deployed, dry run 8/8 clean, **awaiting Chris's click**. 2025 ($6.2k) and 2026 ($2.4k) 66000 rows are small non-tax items — left alone.
Insight recorded: sales-tax recodes are ~NOI-neutral in the app (deduction replaces expense); the withholding recodes are the real double-count removals.

## The exclusion cliff (D30)
Post-cleanup recompute with exclusion still on: 2023 NOI $323,371.85 (real improvement from Chris's hand fixes), but 2024 NOI +$3.7M / 2025 NOI +$9.9M — both cost records gone (wires reclassified away from inventory, payments excluded). Decision D30: remove the exclusion NOW, not at $0.00 — residual error ≤ ~$550k beats an $11M hole. Agent blocked from flipping the env var; Chris pastes it.

## Handed to Chris (in the chat)
1. `/cleanup` → "2024 tax pulls" → Reclassify next 25.
2. `railway variables --set QBO_EXCLUDED_FUNDING_ACCOUNTS=` + `railway up --detach`.
3. Then agent refreshes 2023–2025, runs /checks, reports final annual NOIs.

## Still open (ACH endgame)
14 unmatched payments (~$259k), 8 quarantined conveyor rows (~$292k), overpayment residue → one 68000 entry, then ACH $0.00. Agent admin to be revoked after. Railway token rotation still owed (token appeared in chat 2026-07-15).

## Addendum (later 2026-07-17): feed-refunds bug + income audit
Chris compared the deposits drill-down to QBO's 40100 row ($13,278,636 vs $13,214,772) and asked why. Answer became a real methodology fix: bank-feed customer refunds coded to income accounts were never subtracted (see bank-landed-income-rule.md, "Feed-refunds rule"). Fixed, tested (44/44), deployed, verified live (YTD feedRefunds $61,864.17, 31 rows, sum-checked). Full 3-year income-account audit found only two other patterns, both benign (vendor-discount bill lines; 74200 credit-memo mystery — Chris to look). 2024/2025 also carried this leak ($82k/$48k) — corrected automatically by the cache-generation bump on next statement load. Exclusion env var STILL SET at session pause — Chris has the one-line paste; final 2023–2025 refresh + /checks run happens after that.

## Addendum 2: live verification + the cost-basis discovery
Chris believed the last ACH items were fixed; live verification (full sync, register decomposition ties to the penny: 462 payments −$12,166,200.77 + 446 wires $12,007,064.81 − $0.01 = −$159,135.97) shows NONE of the 15 were touched. Fresh checklist: raw/2026-07-17-ach-final-mile.html (14 payments $258,668.51 + the closing JE ≈ +$99,533 to 68000). txn 31282 ($144,187.97, the last quarantined row) should be LEFT ALONE — its suspected Canon twin is already cleared, so it's likely a real purchase.
Bigger discovery, prompted by Chris ("2023 way too small, 2024/2025 way off" — he was right): the accounting-basis P&L plugs CASH-PAID purchases into the begin+purchases−end formula; the formula wants BILLS-RECEIVED. Cash−bills gaps: 2023 +$211k (paying 2022's A/P), 2024 +$721k, 2025 −$817k (Dec bills paid in 2026). Bills-received NOIs: 2023 $503,742 (matches the $475–510k prediction), 2024 $221,275, 2025 −$408,347 (before no-bill direct purchases). Proposed: accounting-basis statement switches purchases to bills-received + no-bill directs; cash view stays as bank truth. AWAITING CHRIS'S GO. Counts under 2023 also need his confirmation (2022-12-31 $2,660,318 / 2023-12-31 $2,340,174 — implies −$320k shrink).

## Addendum 3 (very late): the accountant's P&L arrives
Chris: counts accurate, "$1M each year", accountant 2025 NOI $1,081,232.95. Session findings: income gap = credit-memo-settled Boise revenue ($579,213, matches our exclusion warning to $1k); COGS gap = vendor-rebate netting (50500, ~$1-1.5M/yr) — see concepts/vendor-rebate-netting-question.md (OPEN; Canon tie-out required before engine change). Expenses agree within $24k. D31 numbers as deployed: 2023 $783,898 / 2024 $510,085 / 2025 −$245,340 / H1-26 $492,762. ACH final-mile checklist delivered (14 payments, none fixed yet per live verification).

## Addendum 4: D33 fits calibration; 2025/2026 "too high" explained; picker + compare shipped
D33 recompute: 2023 $1,540,848 (19.7%) · 2024 $1,019,620 (16.8%) · 2025 $1,645,210 (21.8%) · H1-26 $1,329,956 (21.0%). Chris: "2025 seems way too high… this year seems really high too." Answers, both data-verified: (1) 2025 − the $568k A/P growth (Dec-2025 bills paid in 2026, cost follows payment per his D33 timing rule) ≈ accountant's $1,081,233 — the two agree exactly once timing is accounted; statement now shows an ℹ note whenever billed vs paid diverge >$100k in a range. (2) "Really high" 2026: his 16%-margin intuition was trained on bug-era numbers; true GP has always been ~20% (accountant's 18.5% is SLC+write-downs, consistent); H1-26 also has real growth (revenue +19% YoY) and rough bank-cash corroborates (~$1.0M vs NOI $1.33M). Shipped same turn: Year+Period range picker (2020-present, full/halves/quarters) and P&L "Compare: vs <year>" side-by-side (same period, deltas). /checks re-run still owed.
