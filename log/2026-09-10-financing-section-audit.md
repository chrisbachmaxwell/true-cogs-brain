# 2026-09-10 — Money-map "Cards & payroll dues" audit

Chris: "can you audit the where the money went section and try to close the gaps. the credit card section seems weird to me and having federal taxes there is strange." Both instincts were right. Everything below verified transaction-by-transaction via `/api/register-history?list=1` and `/api/txn-raw` (2026-01-01→08-31).

## Finding 1 — the "strange federal taxes" are Jens's PERSONAL IRS payments

Federal W/H Tax Payable (21002) showed a $142,629 "paydown" in financing. All the routine entries are ~$13–16k biweekly 941 payroll deposits, but two outliers are personal:

- txn **56368**, 2026-04-15, **$58,000** (the 1040 balance-due date), memo `JENS B & ELIZABETH NIE`
- txn **59437**, 2026-06-16, **$67,000** (the Q2 1040-ES date), same memo

Both from Zions, coded to the company payroll-tax account. They belong with the other personal tax prepayments in **Federal Estimated Tax (1040-ES)** (21005, acct id 185), which the money map already counts as money to the owners. This also explains why 21002's balance runs negative (−$152k → −$295k).

## Finding 2 — four card-side AUTOPAY records point at a card instead of the Credit Cards wash (99)

The working pattern (Jan/Feb 2026): card-side autopay credit → wash, bank-side Zions payment → wash; the wash nets zero, the card shows the paydown once. Four 2026 records break it:

| txn | date | amount | what's wrong |
|---|---|---|---|
| 56733 | 04-16 | $37,645.22 | AX Purple credit self-coded to AX Purple → self-cancelling no-op |
| 57898 | 05-16 | $27,402.55 | same |
| 59488 | 06-15 | $20,000.00 | same |
| 58833 | 06-02 | $106,264.84 | PLATINUM Amex credit (memo XXXX3005) coded to **AX Purple** → inflates Purple with money that paid the Platinum card |

Plus one bank-side miscode: **54593** (03-03, $67,918.03) Zions payment coded straight to AX Purple, though its card-side record 54578 shows the money paid the PLATINUM Amex.

**Proof it closes the gap:** the wash's 2026 leftover is **−$123,394.58 = −67,918.03 − 37,645.22 − 27,402.55 + 106,264.84 + ... (the five miscodes, to the penny)**. After the fixes: wash nets to $0 (the measured "Card payment clearing" row disappears), AX Purple's 2026 growth drops from **+$136,447 to +$13,053** (matching Chris's "we've never carried more than $100–150k on cards"), and $125k moves from "Cards & payroll dues" to "Money to the owners". NOI, accounted total, and residual are unchanged — this is composition, not profit.

## What was staged (commit 00fc846, deployed)

- `planReclassify` gained `allowCredit` + per-task `fundedFrom` (card-side autopay records are credit-card credits funded from the card; every other validation unchanged). Tests 50/50.
- New cleanup tasks (in the /cleanup dropdown, dry-run first): `jens-personal-taxes-2026`, `card-autopay-credits-2026-purple`, `card-autopay-credits-2026-amex`; 54593 appended to `card-payments-2025-26-purple`.
- **No QuickBooks write yet — awaiting Chris's go-ahead.** Accountant may prefer `Dist - Jens Nielsen` (30701) over 21005 for the two personal payments; rows carry per-row `to` override if so.

## Still open on the residual

2026 YTD residual $210,703 is the known A/P-paydown timing story (A/P fell $270,801, info-only under pay-when-paid), not new leakage.
