---
type: guide
title: Hack ideas
description: Ways to grow the kit once the loop runs, roughly in order of payoff.
---
# Hack ideas

Write each as a plugin: one file with INSTALL (file changes, one commit), REMOVE (`git revert` of that commit), RULES (in ORCHESTRATOR.md terms). `plugins/canary.md` is the template. Each of these is one evening or less. Keep `ORCHESTRATOR.md` under 60 lines; put detail in `docs/` and link it from `docs/index.md`.

1. **A human gate.** Only you move `draft/ → todo/`. The orchestrator writes the concrete task but stops there. Now nothing runs that you did not read.
2. **A launch TOOL.** `bin/launch <task> <kind>` that does the tab, the wait, the start, the trust dialog and the prompt. Four gotchas in `runners.md`, solved once, and one allow rule covers it.
3. **Evidence.** Before `pane close`, copy the runner's transcript next to the task: `tasks/done/<task>/session.jsonl`. Now you can see what it did.
4. **Retries.** `fail/` items get a `## Fix` section and go back to `todo/` as attempt 2. Cap at 3.
5. **Parallel by dependency.** Add `after: <task>` to frontmatter; only launch when that task is in `done/`.
6. **Provenance.** Every DoD line cites the draft line it came from. Verification gets honest.
7. **Sandbox by default.** Wire `sandboxing.md` into the launch: cwd only, no network, prompts on.
8. **Heartbeat.** Run rounds on a timer instead of typing `run`. The folder already is the state, so a restart costs nothing.
9. **A board view.** A ten-line script that prints `ls tasks/*/` as a table with ages. Or generate `docs/board.md` from it.
10. **Retro.** After five `done/`, ask the orchestrator to read all `## Result` sections and propose three changes to `ORCHESTRATOR.md` as drafts. You decide.

If you want to see where this goes, the `project` skill at github.com/ilkkanisula-g/miniokf is the same idea with all ten done.
