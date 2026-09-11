---
name: canvas
summary: live browser canvas the whole herd posts to, one jsonl behind it
---
# PLUGIN canvas — live browser canvas the whole herd posts to, one jsonl behind it

INSTALL (this session, once; "read plugins/canvas.md and execute")
1. Tree dirty → `git add -A && git commit -qm "pre-plugin canvas"`.
2. Pointer files (`CLAUDE.md`, `AGENTS.md`, `.github/copilot-instructions.md`) lack the line → append to each: `Orchestrator only: also apply RULES of every plugins/*.md.`; `mkdir -p plugins docs/plugins bin`; `.gitignore` lacks `bin/` → append it; `docs/index.md` row: `plugins/` — installed plugins, one registry file each. Commit alone: `git commit -qam "plugins: enable"`. This commit is never reverted.
3. Copy this file to `plugins/canvas.md`.
4. `.gitignore` lacks `canvas.jsonl` → append it. Write `tasks/todo/canvas.md` from CANVAS TASK below, in TASK shape, DOD baseline appended as for any TASK. You do not build it: the next `run` launches a runner and you VERIFY. Until `tasks/done/canvas.md` exists, RULES are a no-op (the TOOLs exit 0 without a server). Nothing else: the TOOLs below are written when first needed, per the TOOL term.
5. Registry `docs/plugins/canvas.md`: frontmatter `type: plugin`, `title: canvas`, `installed: <today>`, `commit: "plugin: canvas — live browser canvas the whole herd posts to, one jsonl behind it"`; body: one line, what it changed, plus: runners post only when their TASK `net:` allows `127.0.0.1`; orchestrator posts always flow.
6. Never edit another plugin's files (`plugins/*.md`, `docs/plugins/*.md`); they must stay revertable. `git add -A && git commit -qm "plugin: canvas — live browser canvas the whole herd posts to, one jsonl behind it"`. Apply RULES from now on, in this session too.

REMOVE ("remove plugin canvas")
- `bin/canvas-down; rm -f bin/canvas-*`. `git revert --no-edit $(git log -1 --format=%H --grep='^plugin: canvas ')`. The revert removes `plugins/canvas.md`, the registry file and the other changes together, whatever happened since. Conflict only in files this plugin created → `git rm` them, `GIT_EDITOR=true git revert --continue`. Any other conflict → stop, show it, let the human decide. `apps/canvas` and `canvas.jsonl` are ignored by git and stay; say so, delete only if asked.

CANVAS TASK (`tasks/todo/canvas.md`; frontmatter `title: canvas`, `runner: claude`, `app: canvas`, `net: [127.0.0.1]`)
## Goal
Two commands in one program under `apps/canvas`, any language, standard library only, no dependencies, no build step, own git repo, README.
- `canvas serve <file>`: HTTP on 127.0.0.1:7777. `POST /` with a JSON body → one MSG appended to `<file>` as one line: keep `from`, `type`, `text`, `task`, `detail`; add `seq` = 1 + lines already in the file and `ts` = UTC ISO now; drop any other key. `from`, `type` or `text` missing → 400, nothing written. `GET /health` → 200 `ok`. Creates the file, never truncates it.
- `canvas ui <file>`: HTTP on 127.0.0.1:7778, no dependency on `serve`. `GET /` → one self-contained HTML page, no CDN, no network beyond itself. `GET /log` → the file bytes. The page fetches `/log` every second and redraws: header = `text` of the latest `type: board` MSG, its `detail` beneath in monospace; one lane per `from`, first-seen order, cards newest at bottom; card = `HH:MM:SS`, `task` if present, `text`, colour by `type` (start blue, progress grey, blocked amber, done green, fail red, board and say neutral), `detail` collapsed under the card; autoscroll unless the user scrolled up; unknown `type` drawn as say.
## DoD
- [ ] `curl -s -d '{"from":"me","type":"say","text":"hi"}' 127.0.0.1:7777/` exits 0 and the file gains exactly one line holding `seq`, `ts`, `from`, `type`, `text`
- [ ] `curl -s -o /dev/null -w '%{http_code}' -d '{"text":"x"}' 127.0.0.1:7777/` prints `400`; file unchanged
- [ ] `curl -s 127.0.0.1:7778/` is HTML that references no external URL; `curl -s 127.0.0.1:7778/log` equals the file
- [ ] `serve` killed → `ui` still renders every line; `serve` restarted → next `seq` continues from the last line
- [ ] a MSG posted while the page is open appears within 2 s, no reload
- [ ] README: how to start each command, the MSG schema, both ports

RULES (in ORCHESTRATOR.md terms; later plugins win on the same step)
TERMS +
- CANVAS: `canvas.jsonl` in the orchestrator folder, one MSG per line, ignored by git, transient. Page at `http://127.0.0.1:7778`.
- MSG: `{"from":"<agent>","type":"<TYPE>","text":"<one line>","task":"<task, optional>","detail":"<longer text, optional>"}`. Server adds `seq`, `ts`. TYPE: say | start | progress | blocked | done | fail | board.
- POST: `bin/canvas-post <from> <type> <text> [task] [detail]`. Never wait on it, never fail a ROUND or a TASK because of it. `from` is always the poster's own name: `orchestrator` for you, its task name for a RUNNER.
- TOOL `bin/canvas-post <from> <type> <text> [task] [detail]`: build MSG from the args with `jq -n --arg` (omit `task`, `detail` when not given or empty), `curl -s -m 2 -d @- 127.0.0.1:7777/ >/dev/null 2>&1 || true`. Fewer than 3 args → usage on stderr, exit 2. Otherwise always exit 0, even with no server.
- TOOL `bin/canvas-up`: `apps/canvas/canvas` missing → exit 0 silently. `curl -s -m 1 127.0.0.1:7777/health` answers → exit 0 silently. Else start `apps/canvas/canvas serve canvas.jsonl` and `apps/canvas/canvas ui canvas.jsonl` with `nohup … >/dev/null 2>&1 &`, paths absolute from the orchestrator folder, print `CANVAS http://127.0.0.1:7778`.
- TOOL `bin/canvas-down`: `pkill -f 'canvas (serve|ui)'`; always exit 0.
ROUND 1 +=
- `bin/canvas-up`.
ROUND 2 +=
- sandbox-claude installed → new TASK frontmatter `net:` += `127.0.0.1`, else the runner's POSTs die silently in the sandbox.
ROUND 3 +=
- POST `orchestrator start "launched <task> on <runner>" <task>`. Runner prompt += `Optional: <abs orchestrator dir>/bin/canvas-post <task> progress "<one-line note>" — never wait on it.`
ROUND 4 +=
- Answering a blocked RUNNER → POST `orchestrator blocked "<what it asked, one line>" <task> "<your answer>"`.
ROUND 5 +=
- POST `orchestrator done|fail "<the LOG line>" <task>`, `detail` = `## Result` for fail.
ROUND 6 +=
- POST `orchestrator board "draft n · todo n · inprogress n · done n · fail n" "" "<ls tasks/*/ output>"`.
