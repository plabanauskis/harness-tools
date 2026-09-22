<div align="center">

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="assets/logo-dark.svg">
  <img src="assets/logo.svg" alt="cchat" width="300">
</picture>

<p><strong>Start a Claude Code chat in a temporary directory.</strong></p>

<p>
  <a href="../../LICENSE"><img alt="License: MIT" src="https://img.shields.io/badge/license-MIT-555"></a>
  <img alt="Platform: Linux · macOS" src="https://img.shields.io/badge/platform-Linux%20%C2%B7%20macOS-555">
  <img alt="Built for Claude Code" src="https://img.shields.io/badge/built%20for-Claude%20Code-D97757">
</p>

</div>

Use `cchat` for questions that do not belong to a project. It creates a temporary
directory, changes into it, and starts Claude Code without changing the working
directory of the shell that launched it.

## Requirements

- `claude` on `PATH`
- Linux or macOS

## Install

Enable it from the [cctools](../../README.md) suite:

```bash
cctools enable cchat
```

## Usage

```bash
cchat                 # start in a new temporary directory
cchat --model opus    # pass arguments to Claude Code unchanged
cchat --help          # show cchat help
```

## Saved sessions

The directory is created under `${TMPDIR:-/tmp}` and remains after the command
ends. This keeps the conversation available to `ccsession` until the directory
is removed, often at reboot.
