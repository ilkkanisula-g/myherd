---
type: reference
title: Sandboxing runners
description: What confines a runner in each harness and on each OS, so you can choose how much to trust one.
---
# Sandboxing runners

A runner edits files and runs commands on its own. Three layers, pick what you need:

| layer | cost | what it stops |
|---|---|---|
| cwd discipline | free | runner starts in `apps/<name>` and sees `tasks/` only through `--add-dir`; the orchestrator folder is not in its path |
| harness permission mode | one flag | prompts before edits or commands; a prompt shows up as herdr `blocked`, and the orchestrator answers it |
| OS sandbox | setup once | writes outside allowed paths and network calls fail even if the model tries |

Never grant "everything" flags (`--dangerously-skip-permissions`, `--allow-all`, `--dangerously-bypass-approvals-and-sandbox`) unless the app directory is disposable and the OS sandbox is on.

## Claude Code

Permission modes: `--permission-mode acceptEdits` (edits free, commands prompt), `auto`, `bypassPermissions`. Scope: `--add-dir`. OS sandbox via settings, passed inline:

```bash
claude --permission-mode acceptEdits --settings '{"sandbox":{"enabled":true,"autoAllowBashIfSandboxed":true,"filesystem":{"allowWrite":["<abs app dir>","<abs tasks dir>"]},"network":{"allowedDomains":[]}}}'
```

Empty `allowedDomains` means no network. Add registries you need, e.g. `registry.npmjs.org`.

## Copilot CLI

Tools: `--allow-tool`, `--deny-tool`, `--allow-all-tools`; paths: `--add-dir`, never `--allow-all-paths`; URLs: `--allow-url`, `--deny-url`. The OS sandbox is a setting, not a flag: start once with `copilot --experimental`, then `/sandbox enable`, `/sandbox status`, `/sandbox policy`. It persists under `sandbox` in `~/.copilot/settings.json`. You set `filesystem` and `network.{allowOutbound,allowLocalNetwork,allowedHosts,blockedHosts}`; the enforcement mode is derived.

- **macOS**: uses the seatbelt already in the OS (`/usr/bin/sandbox-exec`). Nothing to install.
- **WSL**: WSL 2 only, not WSL 1, not Git Bash. Install `sudo apt install -y slirp4netns bubblewrap iptables uidmap` or every sandboxed command dies, `echo` included. Keep the checkout in the WSL filesystem, not `/mnt/c`. LF line endings in scripts. Logins are device codes: open the printed URL in a Windows browser.
- Copying from the TUI: it claims the mouse. `copilot --no-mouse`, or hold Option (iTerm2), Fn (Terminal.app), Shift (Windows Terminal).

## Codex CLI

`-s read-only | workspace-write | danger-full-access` for the filesystem, `-a on-request | never` for approvals, `-c sandbox_workspace_write.network_access=false` to cut network. `--add-dir` for extra writable dirs. Trust for a new directory: `-c 'projects."<abs dir>".trust_level="trusted"'`.

## Host setup, once

```bash
# node 24 via nvm, then Copilot CLI
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/master/install.sh | bash && . ~/.nvm/nvm.sh && nvm install 24
npm install -g @github/copilot
copilot -p "reply with the word OK"          # → OK, after /login once
# Claude Code: https://docs.anthropic.com/claude-code   ·  Codex: npm install -g @openai/codex
# herdr: https://herdr.dev  ·  jq: brew install jq / sudo apt install jq
```

On a fresh machine `copilot` is on PATH only in a new shell after nvm.
