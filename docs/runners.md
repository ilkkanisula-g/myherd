---
type: reference
title: Runners
description: herdr commands the orchestrator uses, the flags per harness, and the four things that go wrong.
---
# Runners

## herdr, the six commands you need

```bash
herdr tab create --cwd <abs dir> --label <name> --no-focus     # → .result.root_pane.pane_id
herdr agent start <name> --kind <kind> --pane <pane> -- <harness flags>
herdr agent prompt <name> "<text>"                            # types it and presses enter
herdr agent get <name>                                        # .result.agent.agent_status: working | blocked | idle | done | unknown
herdr agent read <name> --source visible --format text        # what is on its screen
herdr pane close <pane>
```

Kinds herdr 0.8 recognises: `claude codex copilot pi opencode gemini cursor kiro agy …` (`herdr agent` lists them). `idle` and `done` both mean the agent stopped; check the task file, then verify.

## Flags per harness

| kind | after `--` | what it gives |
|---|---|---|
| `claude` | `--add-dir <abs tasks dir>` | may read and edit its task file outside its cwd; edits inside cwd auto-allowed by your permission mode |
| `claude` | `--permission-mode acceptEdits` | fewer prompts; `bypassPermissions` for none, only for throwaway app dirs |
| `copilot` | `--add-dir <abs tasks dir>` | same idea; `--allow-all-tools` for no prompts, `--no-mouse` if you copy from its screen |
| `codex` | `--no-alt-screen -C <app dir> --add-dir <abs tasks dir> -s workspace-write -a on-request` | keeps output readable by herdr; `-a never` for no prompts |

## Four things that go wrong, measured

1. **Pane not ready.** A fresh pane needs a few seconds before its shell accepts an agent; `agent start` fails with `agent_pane_busy`. Wait, or retry.
2. **Trust dialog eats the prompt.** Claude Code asks "trust this folder?" in a new directory, and a prompt sent then selects "No, exit". Read the screen first; send `down` then `enter` with `herdr agent send-keys`. Codex: pass `-c 'projects."<abs app dir>".trust_level="trusted"'` instead.
3. **First prompt lost.** Codex reports `idle` a moment before its input accepts text. After `prompt`, check `get` shows `working`; if not, send it again.
4. **Empty reads.** herdr 0.8 returns nothing for `--source recent` until output scrolls. Use `--source visible`, or the harness's own transcript: Claude Code `~/.claude/projects/<cwd-slug>/<session>.jsonl` (pin it with `--session-id $(uuidgen)`), Codex `~/.codex/sessions/YYYY/MM/DD/rollout-*.jsonl`.

5. **Classifier denials.** Claude Code in auto mode denies commands that leave the folder or fetch from the network: copying a transcript out of `~/.claude/projects`, `uv run` with a git dependency. Three denials in a row and it asks before everything. Fix: an allow rule in `.claude/settings.json`, or `--add-dir` at start. The sandbox and traceability plugins carry the rules they need.
6. **Ghost text in a runner's input box.** Claude Code shows a predicted next prompt in grey; `herdr agent read` returns it as text. It is nobody's instruction. The orchestrator must verify from files, not from what a pane seems to say.

And one that is not a bug: a runner will tick its own DoD boxes and say done. Verify anyway.

Measured on the kit itself, 2026-09-09: one round with `hello-cli`, Claude Code orchestrating a Claude runner, took 4.5 minutes. The runner asked permission four times under `acceptEdits`; each showed as `blocked` and the orchestrator answered from the task file.
