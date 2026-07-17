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
