# Goals — the loop protocol

One goal file per piece of work, numbered (`01-…`, `02-…`). The CURRENT goal is the lowest-numbered file whose Status is not `DONE`.

## Goal file format
- **Status:** `ACTIVE` / `BLOCKED (waiting on Chris)` / `DONE (dated, ruled by architect)`
- **Done when:** agent-verifiable checkboxes — each must be checkable by running a command or loading a URL, never by opinion.
- **Waiting on Chris:** anything only he can do, ALWAYS with paste-ready commands/clicks and the expected output, written for a non-technical reader.
- **Stop clause:** the condition under which an agent must stop and report instead of pushing on.
- **Iteration log:** dated entries appended by every agent that touches the goal (worker: what was built; tester: findings; architect: rulings).

## The loop
1. **Worker** implements the next unchecked "Done when" item, with tests, runs the build/test gate, pushes, appends to the Iteration log. Never checks its own boxes.
2. **Tester** exercises the live app against the checkboxes like a real user, appends findings. Never fixes code.
3. **Architect** rules each checkbox DONE/NOT DONE with evidence, checks boxes, flips Status when all pass. Never implements.
4. Anyone hitting the Stop clause stops and reports to Chris.

Session-end ritual applies to every role (see CLAUDE.md).
