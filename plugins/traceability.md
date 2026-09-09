---
name: traceability
summary: evidence per run under docs/runs/<task>/<n>/
---
# PLUGIN traceability — evidence per run under docs/runs/<task>/<n>/

INSTALL (this session, once; "read plugins/traceability.md and execute")
1. Tree dirty → `git add -A && git commit -qm "pre-plugin traceability"`.
2. Pointer files (`CLAUDE.md`, `AGENTS.md`, `.github/copilot-instructions.md`) lack the line → append to each: `Orchestrator only: also apply RULES of every plugins/*.md.`; `mkdir -p plugins docs/plugins`; `docs/index.md` row: `plugins/` — installed plugins, one registry file each. Commit alone: `git commit -qam "plugins: enable"`. This commit is never reverted.
3. Copy this file to `plugins/traceability.md`.
4. `mkdir -p docs/runs`; `docs/index.md` row: `runs/` — evidence per task attempt. Claude Code as orchestrator: `.claude/settings.json` → `permissions.allow` += `"Read(~/.claude/projects/**)"`, `"Bash(cp ~/.claude/projects/*)"` (create the file if missing). Without it the classifier denies every transcript copy; the alternative is starting the orchestrator with `claude --add-dir ~/.claude/projects`.
5. Registry `docs/plugins/traceability.md`: frontmatter `type: plugin`, `title: traceability`, `installed: <today>`, `commit: "plugin: traceability — evidence per run under docs/runs/<task>/<n>/"`; body: one line, what it changed.
6. Never edit another plugin's files (`plugins/*.md`, `docs/plugins/*.md`); they must stay revertable. `git add -A && git commit -qm "plugin: traceability — evidence per run under docs/runs/<task>/<n>/"`. Apply RULES from now on, in this session too.

REMOVE ("remove plugin traceability")
- `git revert --no-edit $(git log -1 --format=%H --grep='^plugin: traceability ')`. The revert removes `plugins/traceability.md`, the registry file and the other changes together, whatever happened since. Conflict only in files this plugin created → `git rm` them, `GIT_EDITOR=true git revert --continue`. Any other conflict → stop, show it, let the human decide.

RULES (in ORCHESTRATOR.md terms; later plugins win on the same step)
TERMS +
- TRACE: `docs/runs/<task>/<n>/`, n = attempt, 1 then +1 per LAUNCH of the same task. Never overwrite an earlier n.
- `meta.md` there: frontmatter `type: run`, `task, attempt, runner, pane, agent, session, cwd, flags, started, ended, outcome` (times `date -u +%FT%TZ`). `pane.txt`: `herdr agent read <task> --source visible --format text`, taken before `pane close`. `session.jsonl`: the runner's own transcript, copied.
- SESSION: claude → `~/.claude/projects/<slug>/<id>.jsonl`, slug = cwd with every non-alphanumeric char → `-`, id pinned at LAUNCH; codex → newest `~/.codex/sessions/YYYY/MM/DD/rollout-*.jsonl` modified after `started` whose first line contains cwd; copilot → unknown, write `session: unknown`.
- LOG lines name the attempt: `<task> #<n> launched | blocked | done | fail`.
LAUNCH (claude) +=
`--session-id "$SID"` with `SID=$(uuidgen | tr A-Z a-z)` set before `agent start`.
ROUND 3 +=
- n = 1 + number of dirs in `docs/runs/<task>/`. `mkdir -p` TRACE, write `meta.md` with `ended`, `outcome` empty, `flags` = everything after `--`. `## Run` += `attempt: n`, `trace: docs/runs/<task>/<n>/`.
ROUND 5 +=
- Before `pane close`: write `pane.txt`, copy SESSION → `session.jsonl`, fill `ended`, `outcome` (done | fail). No meta.md yet (launched before this plugin) → create it now from `## Run`. Transcript not found → `session: not-found` in meta; never fail a TASK for it.
