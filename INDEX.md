# Pictureline Cash Reports — Project Brain

**The project:** a Railway-deployed Node.js/TypeScript web app (code repo: `chrisbachmaxwell/true-cogs`) that connects read-only to Pictureline Inc.'s production QuickBooks Online company and produces cash-verified financial reports for any exact date range — an accounting-basis P&L (matching-principle COGS from physical inventory counts), bank reconciliation, cash-flow analysis, and inventory/COGS drill-downs. Built entirely in one long session (2026-07) for Chris Maxwell, the non-technical owner of Pictureline (camera retail, Salt Lake City + Boise). Live at `https://web-production-8c8c0.up.railway.app`.

**Start here, follow links, never sweep folders.**

## Current state
- [entities/project-status.md](entities/project-status.md) — what is built and verified (repo-checked)
- [entities/roadmap.md](entities/roadmap.md) — what is left, ordered by user impact

## Domain knowledge
- [concepts/business-context.md](concepts/business-context.md) — Pictureline's accounts, Lightspeed POS, how money actually flows
- [concepts/accounting-vs-cash-cogs.md](concepts/accounting-vs-cash-cogs.md) — the COGS methodology and why
- [concepts/bank-landed-income-rule.md](concepts/bank-landed-income-rule.md) — what counts as income and the $569k lesson
- [concepts/sales-tax-contra-revenue.md](concepts/sales-tax-contra-revenue.md) — tax-inclusive POS income, tax deducted exactly once
- [concepts/intercompany-boise-credit-memos.md](concepts/intercompany-boise-credit-memos.md) — the SLC↔Boise settlement structure
- [concepts/ledger-hygiene-items.md](concepts/ledger-hygiene-items.md) — known-bad ledger balances for the bookkeeper
- [concepts/qbo-api-invisible-transactions.md](concepts/qbo-api-invisible-transactions.md) — payroll & tax-center are invisible to the API
- [concepts/h1-2026-verified-numbers.md](concepts/h1-2026-verified-numbers.md) — the validated H1 statement, line by line

## Engineering lessons & dead ends
- [concepts/qbo-rate-limits-and-sync-mirror.md](concepts/qbo-rate-limits-and-sync-mirror.md) — throttling, caching, local-first mirror
- [concepts/deploy-race-stale-recompute.md](concepts/deploy-race-stale-recompute.md) — dead end: probing deploys with refresh loops
- [concepts/async-stale-response-guard.md](concepts/async-stale-response-guard.md) — the late-response DOM-overwrite bug
- [concepts/vendor-discount-ratio-fix.md](concepts/vendor-discount-ratio-fix.md) — allocation ratio >1 bug and fix
- [concepts/token-leak-error-sanitization.md](concepts/token-leak-error-sanitization.md) — never stringify raw API errors
- [concepts/permission-classifier-lessons.md](concepts/permission-classifier-lessons.md) — dead ends: force-push, TRUNCATE, credential dumps
- [concepts/security-notes.md](concepts/security-notes.md) — token rotation, auth-bypass prevention, secrets locations

## Decisions
- [entities/decisions.md](entities/decisions.md) — every decision with reasoning and source

## Goals
- [goals/README.md](goals/README.md) — the goal-loop protocol
- [goals/01-auth-gate.md](goals/01-auth-gate.md) — current goal: password sign-in gate live
- [goals/02-pnl-drilldown.md](goals/02-pnl-drilldown.md) — P&L drill-down with self-verifying details
- [goals/03-reconciliation-checks.md](goals/03-reconciliation-checks.md) — proposed: automated tie-out checks

## Roles
- [roles/architect.md](roles/architect.md) · [roles/worker.md](roles/worker.md) · [roles/tester.md](roles/tester.md)

## Chronicle
- [log/](log/) — dated session notes, newest last; start with [log/2026-07-15-brain-created.md](log/2026-07-15-brain-created.md)

## Operations
- [concepts/operations.md](concepts/operations.md) — deploy, env vars, Railway, sync, cache — with exact commands
- [concepts/agent-onboarding.md](concepts/agent-onboarding.md) — paste-ready openers for future sessions
