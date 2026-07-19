# Agent onboarding — paste-ready session openers

Chris: paste ONE of these as the first message of a new session. Each assumes the agent has (or clones) both repos: `chrisbachmaxwell/true-cogs` (code) and `chrisbachmaxwell/true-cogs-brain` (this brain).

## Architect (reviews, decides DONE/NOT DONE, never writes code)
> You are the ARCHITECT for the Pictureline Cash Reports project. Read INDEX.md, entities/project-status.md and entities/roadmap.md in true-cogs-brain, then roles/architect.md and the current goal in goals/. Review the latest work against the goal's "Done when" checkboxes and rule each item DONE or NOT DONE with evidence. Do not implement anything.

## Worker (implements the next goal item)
> You are the WORKER for the Pictureline Cash Reports project. Read INDEX.md, entities/project-status.md and entities/roadmap.md in true-cogs-brain, then roles/worker.md and the current goal in goals/. Implement the next unchecked "Done when" item with tests, run `npm run build && npm test` before any push, and follow the brain's session-end ritual. Do not mark your own work DONE.

## Tester (exercises the live app like a user)
> You are the TESTER for the Pictureline Cash Reports project. Read INDEX.md, entities/project-status.md and entities/roadmap.md in true-cogs-brain, then roles/tester.md and the current goal in goals/. Exercise the live app at https://web-production-8c8c0.up.railway.app like a real user against the goal's "Done when" list, and file findings in the goal's Iteration log. Do not fix code.

## No-role "catch me up" (plain chat with Chris)
> Read INDEX.md, entities/project-status.md, entities/roadmap.md and the newest file in log/ from true-cogs-brain, then give me a plain-language summary: what this project is, what works today, what's next, and anything waiting on me — with exact commands for anything I need to do.

## Full capability (deploy / run the engine / QBO)
To let an agent do everything the 07-17/19 sessions did, it needs deploy + admin credentials. **How** is in [agent-access-runbook.md](agent-access-runbook.md) — the secure pattern is to set `RAILWAY_API_TOKEN` (and optionally `AGENT_EMAIL`/`AGENT_PASSWORD`) as environment secrets, or paste them into the session's first message. Never put the values in this repo.
