---
name: sandbox-claude
summary: claude runners confined by Claude Code's own sandbox
---
# PLUGIN sandbox-claude — claude runners confined by Claude Code's own sandbox

INSTALL (this session, once; "read plugins/sandbox-claude.md and execute")
1. Tree dirty → `git add -A && git commit -qm "pre-plugin sandbox-claude"`.
2. Pointer files (`CLAUDE.md`, `AGENTS.md`, `.github/copilot-instructions.md`) lack the line → append to each: `Orchestrator only: also apply RULES of every plugins/*.md.`; `mkdir -p plugins docs/plugins`; `docs/index.md` row: `plugins/` — installed plugins, one registry file each. Commit alone: `git commit -qam "plugins: enable"`. This commit is never reverted.
3. Copy this file to `plugins/sandbox-claude.md`.
4. `docs/how-it-works.md` += one line: runners of kind claude run under Claude Code's sandbox; see plugins/sandbox-claude.md.
5. Registry `docs/plugins/sandbox-claude.md`: frontmatter `type: plugin`, `title: sandbox-claude`, `installed: <today>`, `commit: "plugin: sandbox-claude — claude runners confined by Claude Code's own sandbox"`; body: one line, what it changed.
6. Never edit another plugin's files (`plugins/*.md`, `docs/plugins/*.md`); they must stay revertable. `git add -A && git commit -qm "plugin: sandbox-claude — claude runners confined by Claude Code's own sandbox"`. Apply RULES from now on, in this session too.

REMOVE ("remove plugin sandbox-claude")
- `git revert --no-edit $(git log -1 --format=%H --grep='^plugin: sandbox-claude ')`. The revert removes `plugins/sandbox-claude.md`, the registry file and the other changes together, whatever happened since. Conflict only in files this plugin created → `git rm` them, `GIT_EDITOR=true git revert --continue`. Any other conflict → stop, show it, let the human decide.

RULES (in ORCHESTRATOR.md terms; later plugins win on the same step)
TERMS +
- TASK: optional frontmatter `net:` = domains the runner may reach, e.g. `[registry.npmjs.org]`. Absent → none.
- TIER: `os-sandbox` when SETTINGS was accepted, else `permission-mode`. `## Run` += `tier:`, `net:` as launched.
- SETTINGS: `{"sandbox":{"enabled":true,"autoAllowBashIfSandboxed":true,"filesystem":{"allowWrite":["<abs app dir>","<abs tasks dir>"]},"network":{"allowedDomains":[<net>]}}}`, one line, absolute paths.
LAUNCH (claude) +=
`--permission-mode acceptEdits --settings '<SETTINGS>'`. Never `bypassPermissions`, never `--dangerously-skip-permissions`.
ROUND 3 +=
- Runner screen says the sandbox is unavailable → `tier: permission-mode`, LOG why once.
ROUND 4 +=
- blocked is a permission prompt: path inside app or tasks, or domain in `net:` → `send-keys enter`. Anything else → `prompt` "Stay inside <app>; do not reach <x>." Second stray request → why under `## Result`, `mv fail/`, close pane. blocked is a question → answer from TASK as before.
