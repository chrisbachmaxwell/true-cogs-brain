# Roadmap — WHAT IS LEFT

**As of: 2026-07-15.** Ordered by what unblocks Chris fastest. Items marked **(Chris)** need his action first — always give him paste-ready commands.

## 1. Sign-in gate live (username + password) — CURRENT GOAL → [../goals/01-auth-gate.md](../goals/01-auth-gate.md)
The dashboard shows Pictureline's full financials and is open to anyone with the URL. Direction changed 2026-07-15 (D24): password-based user management replaces the email magic-link plan — build users table + login + admin user-management UI. **(Chris: choose an admin password when it ships.)** No Resend account needed anymore.

## 2. P&L drill-down → [../goals/02-pnl-drilldown.md](../goals/02-pnl-drilldown.md)
Click any statement amount to see the transactions/accounts behind it, with self-verifying sum-check lines, QBO deep links, and CSV export. Expenses drill down two levels (total → accounts → transactions). Requested by Chris 2026-07-15 (D25).

## 3. Automated reconciliation checks → [../goals/03-reconciliation-checks.md](../goals/03-reconciliation-checks.md)
A "Checks" page running the H1 tie-out invariants (income ≤ bank inflows, income decomposition exact, tax = ledger, direct method ties, drill-down sums match) green/red for any range. Approved by Chris 2026-07-15.

## 4. Rotate the Railway account token **(Chris)**
Token `f406ac63-…` was pasted into a chat session (source: conversation 2026-07-14). Rotate it at railway.com → Account Settings → Tokens once deploys are stable. See [../concepts/security-notes.md](../concepts/security-notes.md).

## 5. Monthly close routine (recurring, small)
Each month-end Chris enters a physical inventory count on the /pnl page; the statement then computes accounting COGS for the new month. Counts exist for 2025-12-31 and 2026-06-30 only. Consider a reminder or a "count missing for last month" banner.

## 6. Bank-ledger CSV reconciliation (parked by Chris: "I'll ask that later")
Chris offered to pull the actual bank ledger so the reconciliation can include payroll (invisible to the QBO API — see [../concepts/qbo-api-invisible-transactions.md](../concepts/qbo-api-invisible-transactions.md)). Build: CSV upload → match against `/api/bank-flow` → itemize the ~$727k H1 "uncategorized" block.

## 7. Boise credit-memo audit (parked by Chris: "we can look into that later")
Two open questions: (a) audit that credit memos issued to Boise match the online-sales/territory/rebate pass-through structure Chris described; (b) explain the credit-memo balance discrepancy — balance sheet shows ~$221k, transaction report totals ~$1.15M. Domain background: [../concepts/intercompany-boise-credit-memos.md](../concepts/intercompany-boise-credit-memos.md).

## 8. Bookkeeper hand-off of ledger-hygiene items
Known-bad balances (sales tax payable −$2.6M, Amex sign flipped, frozen A/P 20200 and A/R 11000, "Fraud" $84k, Cash on Hand −$79,568, ACH −$13M, "AX Purple" typed as liability) documented in [../concepts/ledger-hygiene-items.md](../concepts/ledger-hygiene-items.md). Also: verify the $30,000 deposit coded to Shareholder Distributions #31400 — possibly miscoded **(Chris/bookkeeper)**.

## 9. Merge PR #1
Housekeeping — deploys don't depend on it, but the branch should land on `main` once Chris is happy. PR: https://github.com/chrisbachmaxwell/true-cogs/pull/1

## Explicitly out of scope (agreed)
Multi-company support, per-location P&L splits, writing anything back to QuickBooks (app is read-only by design — decision D2).
