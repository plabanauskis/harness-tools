<div align="center">

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="assets/logo-dark.svg">
  <img src="assets/logo.svg" alt="cobox" width="320">
</picture>

<p><strong>Run Codex autonomously in a sysbox container.</strong></p>

<p>
  <a href="../../LICENSE"><img alt="License: MIT" src="https://img.shields.io/badge/license-MIT-555"></a>
  <img alt="Platform: Linux · amd64" src="https://img.shields.io/badge/platform-Linux%20%C2%B7%20amd64-555">
  <img alt="Built for Codex" src="https://img.shields.io/badge/built%20for-Codex-10A37F">
</p>

</div>

`cobox` runs the host's Codex installation in a Docker container and passes
`--dangerously-bypass-approvals-and-sandbox`. The project and Codex state are
mounted at the same absolute paths they use on the host.

Run it from a Git repository when possible so Git can recover project changes.
Outside a repository, cobox warns that edits have no Git undo and asks before
continuing.

> Requires a Linux amd64 host. The design includes work adapted from
> [RchGrav/claudebox](https://github.com/RchGrav/claudebox) (MIT).

## Safety

Codex has full control inside the container. These host locations are also
writable from the container:

- the selected project;
- `${CODEX_HOME:-$HOME/.codex}`, including file-based authentication;
- shared language caches and the project's inner-Docker data volume.

The host Codex installation and `.gitconfig` are read-only. cobox does not mount
the host Docker socket, SSH keys, GitHub CLI credentials, other projects, or the
rest of the home directory. It does not use privileged mode or host networking.
Sysbox maps container root to an unprivileged host user.

This setup protects ordinary host system files, but it does not protect writable
mounts from mistakes or malicious commands. It also has no network firewall and
does not protect against a kernel or container-runtime vulnerability. Back up
important project and Codex state.

Codex documents the bypass option for externally isolated environments. Do not
use it directly in a normal host shell. See the official
[command documentation](https://learn.chatgpt.com/docs/developer-commands?surface=cli).

## Requirements

1. **Docker:** `docker --version` works without sudo.
2. **sysbox-ce:** install it for Docker and confirm
   `docker info -f '{{.Runtimes}}'` lists `sysbox-runc`.
3. **Codex:** `codex` is on `PATH`. Official npm and native installations are
   supported and mounted read-only.
4. **Authentication:** run Codex on the host so `CODEX_HOME` exists. The host OS
   keyring is not mounted, so the container needs file-based state or
   `OPENAI_API_KEY`/`CODEX_ACCESS_TOKEN`. See the official
   [authentication guide](https://learn.chatgpt.com/docs/auth).

`cobox doctor` reports a failed `codex login status`, but launch remains allowed
because an environment credential may still work.

## Build and install

Enable cobox from the [cotools](../../README.md) suite, then build its image:

```bash
cotools enable cobox
cobox doctor
cobox build
```

The local image defaults to `cobox:latest` and is built for the current username,
UID, GID, and home path. Use `COBOX_IMAGE` for another image name or
`COBOX_SHARE_DIR` for another directory containing the Dockerfile.

The image contains Node 24, Python 3 with `uv`, Go 1.26.x, stable Rust, .NET 10,
Git, GitHub CLI, jq, ripgrep, fd, OpenSSL, socat, and Docker with Compose. Codex
is mounted from the host instead of installed in the image.

## Usage

```bash
cd ~/code/my-project
cobox                         # start Codex in the container
cobox --model gpt-5           # pass arguments to Codex
cobox doctor
cobox version
```

The first argument `build`, `doctor`, `uninstall`, `help`, or `version` selects a
cobox command. Other arguments pass to Codex after the bypass option.

Set `COBOX_NO_DOCKER=1` for faster startup without inner Docker. cobox tints the
terminal while it runs; set `COBOX_TINT` to change the color or
`COBOX_NO_TINT=1` to disable it. The container receives `COBOX=1` and
`COBOX_VERSION`.

## Ports, caches, and inner Docker

Ports `3000-3010` are published by default. Add ports with `COBOX_PORTS`, or put
one port or range per line in `<project>/.cobox/ports`:

```bash
COBOX_PORTS="8080 5173" cobox
```

Services must listen on `0.0.0.0`. The inner Docker daemon starts in the
background; wait until `docker info` succeeds before the first Docker command.

Shared cache volumes are `cobox-npm`, `cobox-cargo`, `cobox-go`, `cobox-uv`, and
`cobox-nuget`. Inner Docker uses `cobox-docker-<project>`.

## Uninstall

```bash
cobox uninstall        # remove the image and shared caches; ask before project volumes
cotools disable cobox  # remove the command link
```

Run `cobox uninstall` before `cotools uninstall` when you also want Docker data
removed. Project volumes are deleted only after confirmation.

## Troubleshooting

| Problem | Fix |
| --- | --- |
| `sysbox runtime not found` | Install sysbox-ce and confirm Docker lists `sysbox-runc`. |
| Host `codex` is missing or unsupported | Install the official npm CLI or a native executable and confirm `command -v codex`. |
| Codex state is missing | Run Codex on the host or set `CODEX_HOME` to an existing directory. |
| `doctor` reports no login | Run `codex login` or pass a supported environment credential. |
| Image is missing | Run `cobox build` or accept the build prompt. |
| Inner Docker is not ready | Wait for `docker info`, or use `COBOX_NO_DOCKER=1`. |
| A host browser cannot reach a service | Bind the service to `0.0.0.0` and publish its port. |
| The non-Git warning appears | Continue only if you accept having no Git undo, or run from a repository. |
