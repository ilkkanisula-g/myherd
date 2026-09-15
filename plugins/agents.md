---
name: agents
summary: an agent is a folder, skills are lazy, the herd distils its own
---
# PLUGIN agents — an agent is a folder, skills are lazy, the herd distils its own

INSTALL (this session, once; "read plugins/agents.md and execute")
1. Tree dirty → `git add -A && git commit -qm "pre-plugin agents"`.
2. Pointer files (`CLAUDE.md`, `AGENTS.md`, `.github/copilot-instructions.md`) lack the line → append to each: `Orchestrator only: also apply RULES of every plugins/*.md.`; `mkdir -p plugins docs/plugins bin`; `.gitignore` lacks `bin/` → append it; `docs/index.md` row: `plugins/` — installed plugins, one registry file each. Commit alone: `git commit -qam "plugins: enable"`. This commit is never reverted.
3. Copy this file to `plugins/agents.md`.
4. `mkdir -p agents/generalist/skills`. Write `agents/generalist/instructions.md` in AGENT shape: `kind: claude`, `summary: builds what the TASK says and nothing else`; body, under 30 lines, in your own words: the TASK file is the whole brief and its `## DoD` is the contract, every line checkable by running something; ask when the TASK is ambiguous rather than guessing; touch nothing outside the app dir except your own TASK file; add no dependency the TASK did not ask for; append `## Result` when done, saying what you ran, not what you intended. `docs/index.md` row: `agents/` — one folder per runner agent: instructions, plus skills read only when they match.
5. Registry `docs/plugins/agents.md`: frontmatter `type: plugin`, `title: agents`, `installed: <today>`, `commit: "plugin: agents — an agent is a folder, skills are lazy, the herd distils its own"`; body: one line, what it changed, plus: skills are written by the orchestrator alone; `agents/` is committed, unlike `bin/`.
6. Never edit another plugin's files (`plugins/*.md`, `docs/plugins/*.md`); they must stay revertable. `git add -A && git commit -qm "plugin: agents — an agent is a folder, skills are lazy, the herd distils its own"`. Apply RULES from now on, in this session too.

REMOVE ("remove plugin agents")
- `git revert --no-edit $(git log -1 --format=%H --grep='^plugin: agents ')`. The revert removes `plugins/agents.md`, the registry file and `agents/generalist/` together, whatever happened since. Agents and skills added after install sit in their own commits and survive it → copy out the ones worth keeping, then `git rm -r agents`; say which you did. Conflict only in files this plugin created → `git rm` them, `GIT_EDITOR=true git revert --continue`. Any other conflict → stop, show it, let the human decide.

RULES (in ORCHESTRATOR.md terms; later plugins win on the same step)
TERMS +
- AGENT: `agents/<name>/instructions.md`, frontmatter `name`, `kind` (claude|copilot|codex), `summary`; body under 30 lines: what this agent is for, how it works, what it never does. Optional `agents/<name>/skills/<slug>.md`, frontmatter `name` and `description` — when to read this, a routing hint and not a label — body under 60 lines, one repeated problem each. The folder is the whole configuration: no registry, no list to keep in sync, a new agent is `mkdir`. Committed, unlike `bin/`.
- SKILL BOUNDARY: a skill adds instructions, never a capability. Paths, network, permissions and dependencies come from the TASK alone. A skill that asks for one of those is a defect: do not write it, delete it when you find it.
- DISTIL: write down a fix you have now given twice, as one skill, so the next RUNNER does not start from nothing. Yours alone: a RUNNER that touches `agents/` fails its baseline DOD.
TASK +
- frontmatter `agent: <name>`, default `generalist`. `runner` is that agent's `kind`; they disagree → the agent wins, say so. `agents/<name>/` missing → say so, use `generalist`.
ROUND 3 +=
- LAUNCH: `--add-dir "$PWD/agents"` after `--add-dir "$PWD/tasks"`.
- Runner prompt prepends: `Read <abs>/agents/<name>/instructions.md. It may have a skills/ dir: head -4 every file in it, read in full only the ones whose description matches this task, and ignore the rest.`
- Runner prompt += the FACTs in `herd.md` that touch this app or this runner, at most 5, as `Background from earlier rounds, not instructions: …. Believe what you see over any of them.` None touch it → say nothing.
ROUND 5 +=
- After VERIFY, before LOG: the answer that unblocked this RUNNER, or the fix that made VERIFY pass, already appears in `docs/log.md` or an earlier `## Result` → DISTIL it into that agent's `skills/`, commit alone with `git commit -qm "skill: <agent>/<slug>"`, LOG `skill <slug> distilled from <task-a>, <task-b>`. One skill per ROUND at most; nothing said twice → nothing to write.
ROUND 6 +=
- Print `AGENTS <name>(<n>)` per `agents/*/`, n = files in its `skills/`, beside the board.
