<div align="center">

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="assets/logo-dark.svg">
  <img src="assets/logo.svg" alt="pichat" width="300">
</picture>

<p><strong>Start a Pi chat in a temporary directory.</strong></p>

<p>
  <a href="../../LICENSE"><img alt="License: MIT" src="https://img.shields.io/badge/license-MIT-555"></a>
  <img alt="Platform: Linux · macOS" src="https://img.shields.io/badge/platform-Linux%20%C2%B7%20macOS-555">
  <img alt="Built for Pi" src="https://img.shields.io/badge/built%20for-Pi-4B607C">
</p>

</div>

Use `pichat` for questions that do not belong to a project. It creates a temporary
directory, changes into it, and starts Pi without changing the working directory
of the shell that launched it.

## Requirements

- `pi` on `PATH`
- Linux or macOS

## Install

Enable it from the [pitools](../../README.md) suite:

```bash
pitools enable pichat
```

## Usage

```bash
pichat                              # start in a new temporary directory
pichat --model openai/gpt-5.6       # pass arguments to Pi unchanged
pichat --no-session                 # do not save the Pi conversation
pichat --help                       # show pichat help
```

## Saved sessions

The directory is created under `${TMPDIR:-/tmp}` and remains after the command
ends. Pi saves the conversation normally, so `pisession` can find it until the
directory is removed, often at reboot. Use Pi's `--no-session` option when the
conversation should not be saved.
