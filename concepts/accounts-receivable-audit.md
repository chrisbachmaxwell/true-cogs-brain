# Accounts Receivable — what it actually is (audited 2026-07-19)

**Chris's question:** "The A/R number may not be real. If it's Boise, real. If it's customers who owe us, real. But if it's Canon or a vendor it may get marked paid but show up as a credit — our accountant uses A/R to track credit memos owed to us and marks them paid later."

**He was right.** New read-only endpoint `/api/ar-audit` pulls every open invoice (Balance > 0),
groups by customer, cross-references the vendor list (fuzzy, suffix-stripped) and captures each
invoice's line descriptions. Result as of 2026-07-19 (A/R $226,424.99, 94 open invoices):

| Bucket | Amount | What it is |
|---|---|---|
| **Vendor rebate/credit trackers** | **$178,787 (79%)** | Canon $80,698 · Nikon $40,517 · Sony $28,134 · ASI/DJI $8,864 · Tamron $7,005 · + more. Descriptions are unambiguous: "IR Claim" (instant rebate), "MDF Funds", "MVP Savings Program", "Vendor Participation", "Spiffs", "Price Protection", "B2B", "Sell-Through Rebate". |
| **Boise intercompany** | **$47,638 (21%)** | "pictureline boise" — real (e.g. "Terrence Campbell Salary while in SLC", "May Inventory Cost"). |
| **Real end-customers owing money** | **$0** | None. |

## Why this does NOT distort profit
- The app counts **bank-landed cash income only** — an unpaid invoice never enters revenue.
- These vendor-rebate "invoices" get settled by applying a **credit memo / vendor credit against a
  bill** (no cash moves), so they never become cash income even when "paid." This is the conduit
  principle (see vendor-rebate-netting-question.md).
- The rebate benefit instead shows up correctly as **lower COGS**: when a vendor credit is applied
  to a bill, Pictureline pays less cash, and the cash-fraction logic (D33) counts only the cash
  paid. So a rebate reduces cost, it is never double-counted as revenue.
- In /money, A/R sits in **"Owed to you (not profit yet)" — info-only, excluded from the proof.**
  The category note now states plainly that it's vendor rebates + Boise, not customer debt.

## Takeaway for Chris
The $226k A/R is not money customers owe and not missing revenue. It's ~$179k of rebates the
brands owe Pictureline (correctly realized as cheaper inventory when the credits are used) plus
~$48k of Boise intercompany. Nothing to chase from customers; nothing wrong with the profit
numbers. If the accountant ever wants a cleaner balance sheet, these could be tracked as vendor
credits rather than customer invoices — a bookkeeping-presentation choice, not a money issue.
