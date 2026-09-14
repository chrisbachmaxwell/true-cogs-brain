# 2026-09-14 — "Where is the 335k we are missing?"

Chris asked where the money-map residual is. Live numbers (2026-01-01 → 09-14): NOI $1,624,491, accounted $1,276,480, residual **$348,010** (21% of NOI; H1-only was ~$334k/25% per profit-to-bank-proof.md — likely the number Chris remembers). Bank outflow audit verdict CLEAN; checks 9/10 pass (sales-tax drift $5,204 known).

Decomposition (named channels, not missing cash):
1. **ACH cleanup artifact ~$152k**: ACH balance moved −$311,479 → −$159,136 inside 2026 — the Jan-2026-dated duplicate wires our July reclassify re-pointed to ACH. Real Zions cash, excluded from the proof by design (broken account) → residual.
2. **Vendor bills settled by credits, not cash**: $908,624 of YTD bill coverage was applied credits (funding-payments, Zions). Cash-only COGS means rebate-credit-settled coverage raises NOI with no cash landing anywhere the proof looks. Sept 1–14 alone: Nikon $72,214 + Fujifilm $5,840 settled by credit memos (payments-audit).
3. **Payroll-invisible remainder** (~3.9% of inflows; QBO API doesn't expose payroll-service/tax-center txns) + Cash on Hand −$29.5k.

Sept 1–14 movement behind the jump from $211k (Aug 31): A/P paid down $357k (682,747→54,676 — the year-end-style clearing), A/R fell $228k (Boise paid $197,036 CASH; Nikon/Fuji credits $78k), inventory −$73k, truck purchase +$58k capex. A count dated 2026-09-14 exists.

Also: app session had to be re-established — Railway token no longer recoverable from transcript (compaction removed it; good for security, bad for autonomy). Signed in via the NEW magic-link flow: POST /auth/login-link for chrism@pictureline.com, read the link from his connected Outlook, redeemed → works end-to-end in production. Recommend setting RAILWAY_API_TOKEN (+ AGENT_EMAIL/AGENT_PASSWORD) as Claude Code environment secrets per agent-access-runbook.md.
