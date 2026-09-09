---
name: visualize
summary: docs/viz.html graph of the docs bundle every round
---
# PLUGIN visualize — docs/viz.html graph of the docs bundle every round

INSTALL (this session, once; "read plugins/visualize.md and execute")
1. Tree dirty → `git add -A && git commit -qm "pre-plugin visualize"`.
2. Pointer files (`CLAUDE.md`, `AGENTS.md`, `.github/copilot-instructions.md`) lack the line → append to each: `Orchestrator only: also apply RULES of every plugins/*.md.`; `mkdir -p plugins docs/plugins`; `docs/index.md` row: `plugins/` — installed plugins, one registry file each. Commit alone: `git commit -qam "plugins: enable"`. This commit is never reverted.
3. Copy this file to `plugins/visualize.md`.
4. `.gitignore` += `docs/viz.html`. Claude Code as orchestrator: `.claude/settings.json` → `permissions.allow` += `"Bash(uv run *)"` (create the file if missing); the first run fetches the visualizer from GitHub and the classifier denies that without a rule. Then run the ROUND 6 command once now, so the first build happens while the human is watching.
5. Registry `docs/plugins/visualize.md`: frontmatter `type: plugin`, `title: visualize`, `installed: <today>`, `commit: "plugin: visualize — docs/viz.html graph of the docs bundle every round"`; body: one line, what it changed.
6. Never edit another plugin's files (`plugins/*.md`, `docs/plugins/*.md`); they must stay revertable. `git add -A && git commit -qm "plugin: visualize — docs/viz.html graph of the docs bundle every round"`. Apply RULES from now on, in this session too.

REMOVE ("remove plugin visualize")
- `git revert --no-edit $(git log -1 --format=%H --grep='^plugin: visualize ')`. The revert removes `plugins/visualize.md`, the registry file and the other changes together, whatever happened since. Conflict only in files this plugin created → `git rm` them, `GIT_EDITOR=true git revert --continue`. Any other conflict → stop, show it, let the human decide.

RULES (in ORCHESTRATOR.md terms; later plugins win on the same step)
TERMS +
- VIZ: `docs/viz.html`, self-contained graph of `docs/`. Nodes = files, edges = markdown links between them. Needs network only when opened (CDN scripts).
- LINK: every new file under `docs/` links at least one other file. No links, no graph. Never edit existing files just to add links; plugin files in particular stay as installed.
ROUND 6 +=
- `uv run --python 3.12 --with "reference-agent @ git+https://github.com/GoogleCloudPlatform/open-knowledge-format@ad30107c31c06aec8a7d5636e0d1058118604e6f" python -m reference_agent visualize --bundle docs --out docs/viz.html --name "$(basename "$PWD")"` → print its last line (concepts, edges, bytes) beside the board. First run installs ~70 packages; later runs take seconds. `uv` missing → say so once, skip.
