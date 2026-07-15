# Rules for this brain repo

This repo is the knowledge base ("brain") for the Pictureline Cash Reports project.
The application lives in `chrisbachmaxwell/true-cogs`. Knowledge lives here.

## The four rules
1. **One lesson per file.**
2. **Update, don't duplicate** — search the vault before writing a new page.
3. **Delete what's wrong** — when reality contradicts a page, fix or delete it
   in the same session, and note the correction in `log/`.
4. **Never touch `raw/`** — it is append-only evidence.

Also: volatile claims carry `(verify after: YYYY-MM)`; every claim carries a
source (conversation date, commit hash, or file path).

## Reading protocol (tiered — don't read everything)
- **Tier 1 (every agent, every session):** `INDEX.md` → `entities/project-status.md` → `entities/roadmap.md`
- **Tier 2:** your role playbook in `roles/`
- **Tier 3:** only the pages INDEX links that are relevant to the task at hand

## Session-end ritual (what keeps this project resumable)
1. Write a dated note in `log/` — what you did, what you learned, what's next.
2. Update `entities/project-status.md` and `entities/roadmap.md` if state changed.
3. Fix any page this session's findings contradicted (rule 3).

## Sync warning
One writer at a time. `git pull` before writing; push when done.
Git only — no auto-sync tools (Dropbox/Drive) on this folder, they corrupt history.
