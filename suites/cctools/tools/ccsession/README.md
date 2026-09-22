<div align="center">

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="assets/logo-dark.svg">
  <img src="assets/logo.svg" alt="ccsession" width="400">
</picture>

<p><strong>Find and resume Claude Code sessions without changing directories first.</strong></p>

<p>
  <a href="../../LICENSE"><img alt="License: MIT" src="https://img.shields.io/badge/license-MIT-555"></a>
  <img alt="Platform: Linux · macOS" src="https://img.shields.io/badge/platform-Linux%20%C2%B7%20macOS-555">
  <img alt="Built for Claude Code" src="https://img.shields.io/badge/built%20for-Claude%20Code-D97757">
</p>

</div>

`ccsession` lists saved Claude Code sessions in an
[`fzf`](https://github.com/junegunn/fzf) picker, newest first. Selecting a live
session changes to its original directory and runs `claude --resume <id>`.

## Picker

```text
  ccsession · 47 sessions · ↑↓ select · / filter · ⏎ resume

  ●  WHEN        DIRECTORY                               BRANCH    SUMMARY
  ─  ──────────  ──────────────────────────────────────  ────────  ──────────────────────────────
  ●  4m ago      ~/source/github/octocat/dashboard       main      Add CSV export to reports
  ●  7h ago      /tmp/cchat.Xa9f2K                       —         Try a parsing approach
  ✗  yesterday   ~/…/old-project                         main      Fix checkout tests
```

The preview shows status, directory, branch, active time, session ID, and
summary. `●` means the original directory exists. `✗` means it is gone; the row
remains visible but cannot be resumed.

## Requirements

- `fzf`, `jq`, and `claude` on `PATH`
- GNU `find`, `grep`, `date`, and `stat`
- Linux, or macOS with GNU tools placed on `PATH`

## Install

Enable it from the [cctools](../../README.md) suite:

```bash
cctools enable ccsession
```

## Usage

```bash
ccsession          # open the picker
ccsession --help   # show help
```

Type to filter, use Up/Down to select, Enter to resume, or Escape to cancel.

## Session discovery

Claude Code stores sessions at
`~/.claude/projects/<encoded-directory>/<session-id>.jsonl`.

| Display field | Source |
| --- | --- |
| Directory | first `cwd` value |
| Branch | first `gitBranch` value |
| Summary | latest `ai-title`; otherwise first user prompt; otherwise ID |
| Active | file modification time |
| Status | whether the recorded directory exists |

Columns use character counts rather than byte counts so Unicode symbols align
under a UTF-8 locale.

## Tests

From the repository root:

```bash
bash suites/cctools/tools/ccsession/tests/ccsession.test.sh
```
