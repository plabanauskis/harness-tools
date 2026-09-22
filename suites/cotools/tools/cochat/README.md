<div align="center">

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="assets/logo-dark.svg">
  <img src="assets/logo.svg" alt="cochat" width="300">
</picture>

<p><strong>Start a Codex chat in a temporary directory.</strong></p>

<p>
  <a href="../../LICENSE"><img alt="License: MIT" src="https://img.shields.io/badge/license-MIT-555"></a>
  <img alt="Platform: Linux · macOS" src="https://img.shields.io/badge/platform-Linux%20%C2%B7%20macOS-555">
  <img alt="Built for Codex" src="https://img.shields.io/badge/built%20for-Codex-10A37F">
</p>

</div>

Use `cochat` for questions that do not belong to a project. It creates a temporary
directory, changes into it, and starts Codex without changing the working
directory of the shell that launched it.

## Requirements

- `codex` on `PATH`
- Linux or macOS

## Install

Enable it from the [cotools](../../README.md) suite:

```bash
cotools enable cochat
```

## Usage

```bash
cochat                       # start in a new temporary directory
cochat --model gpt-5         # pass arguments to Codex unchanged
cochat --help                # show cochat help
```

## Saved sessions

The directory is created under `${TMPDIR:-/tmp}` and remains after the command
ends. This keeps the conversation available to `cosession` until the directory
is removed, often at reboot.
