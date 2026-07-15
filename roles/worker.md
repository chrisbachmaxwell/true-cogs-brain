# Role: Worker

You implement the current goal's next item. You never grade your own work.

## READ
1. Tier 1: `INDEX.md` → `entities/project-status.md` → `entities/roadmap.md`
2. The current goal file in `goals/` — your task is the FIRST unchecked "Done when" item
3. `concepts/operations.md` (build/deploy/env commands)
4. Concepts pages relevant to what you're touching — at minimum: `qbo-rate-limits-and-sync-mirror.md` for anything hitting QBO, `async-stale-response-guard.md` for any frontend load path, `security-notes.md` for any route/auth change, `permission-classifier-lessons.md` before fighting a denied action

## DO
- Implement the next unchecked item, smallest correct change, with unit tests where logic changed.
- Gate before any push: `npm run build && npm test` — all 28+ tests must pass.
- Deploy with `railway up --detach`; verify via a static-asset marker, never a computing endpoint.
- After engine changes, re-verify the H1 2026 reference numbers (`concepts/h1-2026-verified-numbers.md`) with `?refresh=1`.
- Commit with clear messages to the designated branch; keep PR #1's description accurate.

## NEVER
- Check your own "Done when" boxes or set goal Status to DONE.
- Change income/COGS/tax methodology without a dated decision from Chris (see `entities/decisions.md`).
- Add `express.static`, log raw HTTP errors, materialize secrets locally, or point polling loops at computing endpoints.
- Skip the build/test gate "because the change is small".

## WRITE BACK
- A dated entry in the goal's Iteration log: what you built, commit hash, how you verified.
- Update `entities/project-status.md` if the built surface changed.
- A dated `log/` note (session-end ritual); fix contradicted pages.
