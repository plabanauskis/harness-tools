<div align="center">

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="assets/logo-dark.svg">
  <img src="assets/logo.svg" alt="pibox" width="320">
</picture>

<p><strong>Run Pi in a sysbox container.</strong></p>

<p>
  <a href="../../LICENSE"><img alt="License: MIT" src="https://img.shields.io/badge/license-MIT-555"></a>
  <img alt="Platform: Linux · amd64" src="https://img.shields.io/badge/platform-Linux%20%C2%B7%20amd64-555">
  <img alt="Built for Pi" src="https://img.shields.io/badge/built%20for-Pi-4B607C">
</p>

</div>

`pibox` runs the host's Pi installation as the host user in a Docker container.
Pi [already runs tools with full permissions](https://pi.dev), so pibox does not
add an approval or sandbox bypass option. Built-in tools, `!` commands, and
extension tools all run inside the container.

Run it from a Git repository when possible so Git can recover project changes.
Outside a repository, pibox warns that edits have no Git undo and asks before
continuing.

> Requires a Linux amd64 host. The design includes work adapted from
> [RchGrav/claudebox](https://github.com/RchGrav/claudebox) (MIT) and follows
> Pi's [container guide](https://github.com/earendil-works/pi-mono/blob/main/packages/coding-agent/docs/containerization.md).

## Safety

Pi has full control inside the container. These host locations are also writable
from the container:

- the selected project;
- all of `$HOME/.pi`, including authentication, settings, packages, sessions,
  extensions, databases, and caches;
- custom `PI_CODING_AGENT_DIR` or `PI_CODING_AGENT_SESSION_DIR` locations when
  they are outside `$HOME/.pi`;
- shared language caches and the project's inner-Docker data volume.

The complete `$HOME/.pi` mount is intentional. Packages can store data beside
`~/.pi/agent`, and mounting only known directories would lose new package data.
Extensions have the same access as the main Pi process.

The host Pi installation and `.gitconfig` are read-only. pibox does not mount the
host Docker socket, SSH keys, GitHub CLI credentials, other projects, or the rest
of the home directory. It does not use privileged mode or host networking.
Sysbox maps container root to an unprivileged host user.

This setup protects ordinary host system files, but it does not protect writable
mounts from mistakes or malicious commands. It also has no network firewall and
does not protect against a kernel or container-runtime vulnerability. Back up
important project and Pi state.

## Requirements

1. **Docker:** `docker --version` works without sudo.
2. **sysbox-ce:** install it for Docker and confirm
   `docker info -f '{{.Runtimes}}'` lists `sysbox-runc`.
3. **Pi:** `pi` is on `PATH` and comes from
   `@earendil-works/pi-coding-agent` or an official compiled Pi directory.
4. **Pi state:** run Pi on the host so
   `${PI_CODING_AGENT_DIR:-$HOME/.pi/agent}` exists.
5. **Authentication:** use `/login` in host Pi or export a supported provider
   credential. `pibox doctor` warns when `auth.json` is empty but does not block
   launch because local and custom providers may not use it.

## Build and install

Enable pibox from the [pitools](../../README.md) suite, then build its image:

```bash
pitools enable pibox
pibox doctor
pibox build
```

The local image defaults to `pibox:latest` and is built for the current username,
UID, GID, and home path. Use `PIBOX_IMAGE` for another image name or
`PIBOX_SHARE_DIR` for another directory containing the Dockerfile.

The image contains Node 24, Python 3 with `uv`, Go 1.26.x, stable Rust, .NET 10,
Git, GitHub CLI, jq, ripgrep, fd, OpenSSL, socat, and Docker with Compose. Pi is
mounted from the host instead of installed in the image.

## Usage

```bash
cd ~/code/my-project
pibox                                  # start Pi in the container
pibox --model anthropic/claude-sonnet  # pass arguments to Pi
pibox --no-extensions
pibox doctor
pibox version
```

The first argument `build`, `doctor`, `uninstall`, `help`, or `version` selects a
pibox command. Other arguments pass to Pi unchanged. `PI_SKIP_VERSION_CHECK=1`
is set in the container because the mounted host installation supplies Pi.

Set `PIBOX_NO_DOCKER=1` for faster startup without inner Docker. pibox tints the
terminal while it runs; set `PIBOX_TINT` to change the color or
`PIBOX_NO_TINT=1` to disable it. The container receives `PIBOX=1` and
`PIBOX_VERSION`.

### Provider environment

Mounted `auth.json` is preferred. pibox forwards a fixed list of provider
credentials for Pi's built-in providers, including Anthropic, OpenAI/Azure,
Google, Bedrock, OpenRouter, Cloudflare, Mistral, Groq, Cerebras, xAI, DeepSeek,
NVIDIA, ZAI, Qwen, MiniMax, Xiaomi, and Kimi. It also forwards proxy settings.
It does not copy the full host environment or credential directories such as
`~/.aws`.

## Ports, caches, and inner Docker

Ports `3000-3010` are published by default. Add ports with `PIBOX_PORTS`, or put
one port or range per line in `<project>/.pibox/ports`:

```bash
PIBOX_PORTS="8080 5173" pibox
```

Services must listen on `0.0.0.0`. The inner Docker daemon starts in the
background; wait until `docker info` succeeds before the first Docker command.

Shared cache volumes are `pibox-npm`, `pibox-cargo`, `pibox-go`, `pibox-uv`, and
`pibox-nuget`. Inner Docker uses `pibox-docker-<project>`.

## Uninstall

```bash
pibox uninstall        # remove the image and shared caches; ask before project volumes
pitools disable pibox  # remove the command link
```

Run `pibox uninstall` before `pitools uninstall` when you also want Docker data
removed. Project volumes are deleted only after confirmation.

## Troubleshooting

| Problem | Fix |
| --- | --- |
| `sysbox runtime not found` | Install sysbox-ce and confirm Docker lists `sysbox-runc`. |
| Host Pi layout is unsupported | Install the current npm package or use an official compiled Pi directory. |
| Pi state is missing | Run Pi on the host or set `PI_CODING_AGENT_DIR` to an existing directory. |
| Custom session directory is missing | Create `PI_CODING_AGENT_SESSION_DIR` before launching. |
| `doctor` reports no authentication | Use `/login` in host Pi or pass a supported provider credential. |
| Image is missing | Run `pibox build` or accept the build prompt. |
| Inner Docker is not ready | Wait for `docker info`, or use `PIBOX_NO_DOCKER=1`. |
| A host browser cannot reach a service | Bind the service to `0.0.0.0` and publish its port. |
| The non-Git warning appears | Continue only if you accept having no Git undo, or run from a repository. |
