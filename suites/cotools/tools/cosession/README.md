<div align="center">

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="assets/logo-dark.svg">
  <img src="assets/logo.svg" alt="cosession" width="400">
</picture>

<p><strong>Find and resume Codex sessions without changing directories first.</strong></p>

<p>
  <a href="../../LICENSE"><img alt="License: MIT" src="https://img.shields.io/badge/license-MIT-555"></a>
  <img alt="Platform: Linux · macOS" src="https://img.shields.io/badge/platform-Linux%20%C2%B7%20macOS-555">
  <img alt="Built for Codex" src="https://img.shields.io/badge/built%20for-Codex-10A37F">
</p>

</div>

`cosession` lists saved Codex sessions in an
[`fzf`](https://github.com/junegunn/fzf) picker, newest first. Selecting a live
session changes to its original directory and runs `codex resume <session-id>`.

## Picker

```text
  cosession · 47 sessions · ↑↓ select · / filter · ⏎ resume

  ●  WHEN        DIRECTORY                               BRANCH    SUMMARY
  ─  ──────────  ──────────────────────────────────────  ────────  ──────────────────────────────
  ●  4m ago      ~/source/github/octocat/dashboard       main      Add CSV export to reports
  ●  7h ago      /tmp/cochat.Xa9f2K                      —         Try a parsing approach
  ✗  yesterday   ~/…/old-project                         main      Fix checkout tests
```

The preview shows status, directory, branch, source, active time, session ID, and
summary. `●` means the original directory exists. `✗` means it is gone; the row
remains visible but cannot be resumed.

## Requirements

- `fzf`, `jq`, and `codex` on `PATH`
- Linux or macOS

The script supports the standard GNU tools on Linux and BSD `date` and `stat` on
macOS; GNU coreutils are not required on macOS.

## Install

Enable it from the [cotools](../../README.md) suite:

```bash
cotools enable cosession
```

## Usage

```bash
cosession          # open the picker
cosession --help   # show help
```

Type to filter, use Up/Down to select, Enter to resume, or Escape to cancel.

## Session discovery

The state directory is `${CODEX_HOME:-$HOME/.codex}`. `cosession` recursively
reads `sessions/**/rollout-*.jsonl` and includes root sessions from Codex CLI and
Codex Desktop. It skips internal-agent sessions and malformed files.

| Display field | Source |
| --- | --- |
| ID, directory, branch, source | rollout `session_meta` |
| Active | rollout file modification time |
| Summary | `session_index.jsonl`; then `history.jsonl`; then first user input; then ID |
| Status | whether the recorded directory exists |

Injected environment messages are not used as summaries.

## Tests

From the repository root:

```bash
bash suites/cotools/tools/cosession/tests/cosession.test.sh
```
