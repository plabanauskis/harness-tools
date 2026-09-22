<div align="center">

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="assets/logo-dark.svg">
  <img src="assets/logo.svg" alt="ccbox" width="320">
</picture>

<p><strong>Run Claude Code autonomously in a sysbox container.</strong></p>

<p>
  <a href="../../LICENSE"><img alt="License: MIT" src="https://img.shields.io/badge/license-MIT-555"></a>
  <img alt="Platform: Linux · amd64" src="https://img.shields.io/badge/platform-Linux%20%C2%B7%20amd64-555">
  <img alt="Built for Claude Code" src="https://img.shields.io/badge/built%20for-Claude%20Code-D97757">
</p>

</div>

`ccbox` runs the host's Claude Code installation in a Docker container with
`--dangerously-skip-permissions`. The project and `~/.claude` are mounted at the
same absolute paths they use on the host. Sessions, plugins, configuration, and
Git identity therefore work without path conversion.

Run it from a Git repository when possible so Git can recover project changes.
Outside a repository, ccbox warns that edits have no Git undo and asks before
continuing.

> Requires a Linux amd64 host. The design includes work adapted from
> [RchGrav/claudebox](https://github.com/RchGrav/claudebox) (MIT).

## Safety

Claude Code has full control inside the container. These host locations are also
writable from the container:

- the selected project;
- `~/.claude`, including sessions, plugins, settings, and file-based credentials;
- shared language caches and the project's inner-Docker data volume.

The host Claude Code installation and `.gitconfig` are read-only. ccbox does not
mount the host Docker socket, SSH keys, GitHub CLI credentials, other projects,
or the rest of the home directory. It does not use privileged mode or host
networking. Sysbox maps container root to an unprivileged host user.

This setup protects ordinary host system files such as `/usr`, `/etc`, and
`/var`, but it does not protect writable mounts from mistakes or malicious
commands. It also has no network firewall and does not protect against a kernel
or container-runtime vulnerability. Back up important project and Claude state.

## Requirements

1. **Docker:** `docker --version` works without sudo.
2. **sysbox-ce:** install it for Docker and confirm
   `docker info -f '{{.Runtimes}}'` lists `sysbox-runc`. Kernel 5.12 or newer is
   required for ID-mapped mounts. Packages are available from the
   [sysbox releases](https://github.com/nestybox/sysbox/releases).
3. **Claude Code:** `claude` is on `PATH` and uses the native installer under
   `~/.local/share/claude`. npm-global installations are not supported.
4. **Authentication:** log in on the host so
   `~/.claude/.credentials.json` exists, or pass `ANTHROPIC_API_KEY`.

Run `ccbox doctor` to check these requirements.

## Build and install

Enable ccbox from the [cctools](../../README.md) suite, then build its image:

```bash
cctools enable ccbox
ccbox doctor
ccbox build
```

The local image defaults to `ccbox:latest` and is built for the current username,
UID, GID, and home path. Use `CCBOX_IMAGE` for another image name or
`CCBOX_SHARE_DIR` for another directory containing the Dockerfile. A typical
build takes about five minutes and uses about 5 GB.

The image contains Node 24, Python 3 with `uv`, Go 1.26.x, stable Rust, .NET 10,
Git, GitHub CLI, jq, ripgrep, fd, OpenSSL, socat, and Docker with Compose. Claude
Code is mounted from the host instead of installed in the image.

## Usage

```bash
cd ~/code/my-project
ccbox                  # start Claude Code in the container
ccbox --model opus     # pass arguments to Claude Code
ccbox doctor
ccbox version
```

The first argument `build`, `doctor`, `uninstall`, `help`, or `version` selects a
ccbox command. Other arguments pass to Claude Code after
`--dangerously-skip-permissions`.

Set `CCBOX_NO_DOCKER=1` for faster startup without inner Docker. ccbox tints the
terminal while it runs; set `CCBOX_TINT` to change the color or
`CCBOX_NO_TINT=1` to disable it. The container receives `CCBOX=1` and
`CCBOX_VERSION`.

## Ports, caches, and inner Docker

Ports `3000-3010` are published by default. Add ports with `CCBOX_PORTS`, or put
one port or range per line in `<project>/.ccbox/ports`:

```bash
CCBOX_PORTS="8080 5173" ccbox
```

Services must listen on `0.0.0.0`. The inner Docker daemon starts in the
background; wait until `docker info` succeeds before the first Docker command.

Shared cache volumes are `ccbox-npm`, `ccbox-cargo`, `ccbox-go`, `ccbox-uv`, and
`ccbox-nuget`. Inner Docker uses `ccbox-docker-<project>`.

## Uninstall

```bash
ccbox uninstall        # remove the image and shared caches; ask before project volumes
cctools disable ccbox  # remove the command link
```

Run `ccbox uninstall` before `cctools uninstall` when you also want Docker data
removed. Project volumes are deleted only after confirmation. The command may
also offer to remove obsolete `~/.config/ccbox` files from ccbox 1.x.

## Troubleshooting

| Problem | Fix |
| --- | --- |
| `sysbox runtime not found` | Install sysbox-ce and confirm Docker lists `sysbox-runc`. |
| Host `claude` is missing | Install Claude Code and confirm `command -v claude`. |
| Host Claude layout is unsupported | Replace an npm-global installation with the native installer. |
| Claude asks you to log in | Make sure `~/.claude/.credentials.json` is writable, or pass `ANTHROPIC_API_KEY`. |
| Files have the wrong owner | Run `ccbox build` again for the current UID and GID. |
| Image is missing | Run `ccbox build` or accept the build prompt. |
| Inner Docker is not ready | Wait for `docker info`, or use `CCBOX_NO_DOCKER=1`. |
| A host browser cannot reach a service | Bind the service to `0.0.0.0` and publish its port. |
| The non-Git warning appears | Continue only if you accept having no Git undo, or run from a repository. |

See the [approved redesign document](docs/superpowers/specs/2026-06-22-ccbox-redesign-design.md)
for the original design decisions and test results.
