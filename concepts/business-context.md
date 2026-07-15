# Business context — how Pictureline's money actually flows

Source: Chris's explanations in conversation, 2026-07. Essential domain knowledge for any financial work here.

- **Pictureline Inc.** — camera/photo retailer, two locations: Salt Lake City (main company, this QBO file) and **Boise** (separate entity).
- **Lightspeed POS** tracks all retail sales and syncs **tax-INCLUSIVE daily totals** into income account **40100**. Sales tax is not split out at sync time — this is why income is counted tax-inclusive and remittances are deducted once (decision D8).
- **Chart of accounts that matter:**
  - `11900` Material Inventory (SLC) · `11901` Boise Inventory — the two tracked inventory assets
  - `40100` retail income (Lightspeed target) · `21900` sales tax payable (configured tax account)
  - `51300` freight · `50600` customer repairs · `50200` materials — direct costs, part of COGS (D10)
  - `11300` Credit Memos — an **asset ledger of manufacturer rebate credit** (not customer credit memos). Unused balance pays for future inventory.
  - `31400` Shareholder Distributions — a $30,000 deposit was coded here; flagged as possibly miscoded, Chris to verify.
- **SLC buys all inventory and claims all manufacturer rebates** for both locations. Boise settles with SLC via cash transfers AND credit memos (see [intercompany-boise-credit-memos.md](intercompany-boise-credit-memos.md)).
- **Vendor bills often carry negative discount lines** ("the bill is larger than what is actually paid… split category") — see [vendor-discount-ratio-fix.md](vendor-discount-ratio-fix.md).
- **Vendor credits are applied at bill-payment time**, so payment lines are already net of credits.
- **Monthly journal entries** in the file are intercompany bookkeeping — Chris distrusts them for P&L purposes; the app deliberately ignores JEs for income (warns instead).
- **Payroll** is run through QBO Payroll and **is invisible to the QBO API** — see [qbo-api-invisible-transactions.md](qbo-api-invisible-transactions.md).
- Boise physical inventory reference points Chris gave: began H1-2026 at $273,270.49, ended at $482,542.88 (source: conversation).
