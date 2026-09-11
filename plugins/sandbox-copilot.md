---
name: sandbox-copilot
summary: copilot runners confined by Copilot CLI's sandbox and tool gates
---
# PLUGIN sandbox-copilot — copilot runners confined by Copilot CLI's sandbox and tool gates

INSTALL (this session, once; "read plugins/sandbox-copilot.md and execute")
1. Tree dirty → `git add -A && git commit -qm "pre-plugin sandbox-copilot"`.
2. Pointer files (`CLAUDE.md`, `AGENTS.md`, `.github/copilot-instructions.md`) lack the line → append to each: `Orchestrator only: also apply RULES of every plugins/*.md.`; `mkdir -p plugins docs/plugins bin`; `.gitignore` lacks `bin/` → append it; `docs/index.md` row: `plugins/` — installed plugins, one registry file each. Commit alone: `git commit -qam "plugins: enable"`. This commit is never reverted.
3. Copy this file to `plugins/sandbox-copilot.md`.
4. `docs/how-it-works.md` += one line: runners of kind copilot run under Copilot CLI's sandbox; HOST setup in plugins/sandbox-copilot.md.
5. Registry `docs/plugins/sandbox-copilot.md`: frontmatter `type: plugin`, `title: sandbox-copilot`, `installed: <today>`, `commit: "plugin: sandbox-copilot — copilot runners confined by Copilot CLI's sandbox and tool gates"`; body: one line, what it changed.
6. Never edit another plugin's files (`plugins/*.md`, `docs/plugins/*.md`); they must stay revertable. `git add -A && git commit -qm "plugin: sandbox-copilot — copilot runners confined by Copilot CLI's sandbox and tool gates"`. Apply RULES from now on, in this session too.

REMOVE ("remove plugin sandbox-copilot")
- `git revert --no-edit $(git log -1 --format=%H --grep='^plugin: sandbox-copilot ')`. The revert removes `plugins/sandbox-copilot.md`, the registry file and the other changes together, whatever happened since. Conflict only in files this plugin created → `git rm` them, `GIT_EDITOR=true git revert --continue`. Any other conflict → stop, show it, let the human decide.

RULES (in ORCHESTRATOR.md terms; later plugins win on the same step)
TERMS +
- TASK: optional frontmatter `net:` = domains the runner may reach. Absent → none.
- HOST, once per machine, not per TASK: `copilot --experimental`, then `/sandbox enable`, `/sandbox status`, `/sandbox policy`. Persists under `sandbox` in `~/.copilot/settings.json`. macOS: seatbelt built in, nothing to install. WSL 2 only: first `sudo apt install -y slirp4netns bubblewrap iptables uidmap`, checkout inside the WSL filesystem, LF line endings.
- TIER: `os-sandbox` when `/sandbox status` says enabled on this host, else `tool-gate`. `## Run` += `tier:`, `net:`.
LAUNCH (copilot) +=
`--allow-url <d>` once per `net:` entry. `--allow-tool <t>` / `--deny-tool <t>` only for tools the TASK names. Never `--allow-all`, `--allow-all-paths`, `--allow-all-tools`.
ROUND 3 +=
- HOST not done → `tier: tool-gate`, LOG it once.
ROUND 4 +=
- blocked is a tool or URL prompt: inside app or tasks, or in `net:` → `send-keys enter`. Else `prompt` "Stay inside <app>." Second stray request → why under `## Result`, `mv fail/`, close pane. blocked is a question → answer from TASK as before.
