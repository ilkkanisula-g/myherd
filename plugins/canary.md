---
name: canary
summary: prove plugins load and unload
---
# PLUGIN canary — prove plugins load and unload

INSTALL (this session, once; "read plugins/canary.md and execute")
1. Tree dirty → `git add -A && git commit -qm "pre-plugin canary"`.
2. Pointer files (`CLAUDE.md`, `AGENTS.md`, `.github/copilot-instructions.md`) lack the line → append to each: `Orchestrator only: also apply RULES of every plugins/*.md.`; `mkdir -p plugins docs/plugins bin`; `.gitignore` lacks `bin/` → append it; `docs/index.md` row: `plugins/` — installed plugins, one registry file each. Commit alone: `git commit -qam "plugins: enable"`. This commit is never reverted.
3. Copy this file to `plugins/canary.md`.
4. Nothing else.
5. Registry `docs/plugins/canary.md`: frontmatter `type: plugin`, `title: canary`, `installed: <today>`, `commit: "plugin: canary — prove plugins load and unload"`; body: one line, what it changed.
6. Never edit another plugin's files (`plugins/*.md`, `docs/plugins/*.md`); they must stay revertable. `git add -A && git commit -qm "plugin: canary — prove plugins load and unload"`. Apply RULES from now on, in this session too.

REMOVE ("remove plugin canary")
- `git revert --no-edit $(git log -1 --format=%H --grep='^plugin: canary ')`. The revert removes `plugins/canary.md`, the registry file and the other changes together, whatever happened since. Conflict only in files this plugin created → `git rm` them, `GIT_EDITOR=true git revert --continue`. Any other conflict → stop, show it, let the human decide.

RULES (in ORCHESTRATOR.md terms; later plugins win on the same step)
ROUND 1 +=
- Print `PLUGIN OK canary` beside the board. That is all this plugin does; it proves plugins load, and `remove plugin canary` proves they unload.
