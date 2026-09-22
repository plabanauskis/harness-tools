<div align="center">

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="assets/logo-dark.svg">
  <img src="assets/logo.svg" alt="pisession" width="400">
</picture>

<p><strong>Find and resume Pi sessions without changing directories first.</strong></p>

<p>
  <a href="../../LICENSE"><img alt="License: MIT" src="https://img.shields.io/badge/license-MIT-555"></a>
  <img alt="Platform: Linux · macOS" src="https://img.shields.io/badge/platform-Linux%20%C2%B7%20macOS-555">
  <img alt="Built for Pi" src="https://img.shields.io/badge/built%20for-Pi-4B607C">
</p>

</div>

`pisession` lists saved Pi sessions in an
[`fzf`](https://github.com/junegunn/fzf) picker, newest first. Selecting a live
session changes to its recorded directory and runs `pi --session <file>`.
Pi's own `pi -r` and `/resume` commands remain useful within one project;
`pisession` provides a global list.

## Picker

```text
  pisession · 47 sessions · ↑↓ select · / filter · ⏎ resume

  ●  WHEN        DIRECTORY                               MODEL                 SUMMARY
  ─  ──────────  ──────────────────────────────────────  ────────────────────  ──────────────────────────────
  ●  4m ago      ~/source/github/octocat/dashboard       anthropic/claude-so…  Add CSV export to reports
  ●  7h ago      /tmp/pichat.Xa9f2K                      openai-codex/gpt-5.6  Try a parsing approach
  ✗  yesterday   ~/…/old-project                         google/gemini-3-pro   Fix checkout tests
```

The preview shows status, model, Pi session version, active time, session ID, and
summary. `●` means the original directory exists. `✗` means it is gone; the row
remains visible but cannot be resumed.

## Requirements

- `fzf`, `jq`, and `pi` on `PATH`
- Linux or macOS

The script supports each platform's standard `date` and `stat` commands.

## Install

Enable it from the [pitools](../../README.md) suite:

```bash
pitools enable pisession
```

## Usage

```bash
pisession          # open the picker
pisession --help   # show help
```

Type to filter, use Up/Down to select, Enter to resume, or Escape to cancel.

## Session discovery

The default session directory is:

```text
${PI_CODING_AGENT_DIR:-$HOME/.pi/agent}/sessions
```

`PI_CODING_AGENT_SESSION_DIR` overrides it. `pisession` recursively finds JSONL
files with a valid Pi session header and skips malformed or unrelated files.

| Display field | Source |
| --- | --- |
| ID, directory, version | `type: "session"` header |
| Model | latest `model_change`; otherwise latest assistant provider/model |
| Active | file modification time |
| Summary | latest `session_info.name`; then first user text; then ID |
| Status | whether the recorded directory exists |

The exact file path is passed to Pi when resuming.

## Tests

From the repository root:

```bash
bash suites/pitools/tools/pisession/tests/pisession.test.sh
```
