# ccbox redesign: use host paths inside the container

**Date:** 2026-06-22

**Status:** Approved design (pre-implementation)

**Supersedes:** decisions 5 and 6 in the original `ccbox-implementation-plan.md`,
plus its container-user and workspace-path choices.

> This is a historical design record. See the tool README for current behavior.

## 1. Goal and limits

Run Claude Code with `--dangerously-skip-permissions` in a container that prevents
normal commands from changing the host operating system.

The container must protect installed packages, system services, `/usr`, `/etc`,
`/var`, and all host files that are not mounted. It is not intended to:

- protect the mounted project or Claude state;
- give the container GitHub access;
- restrict network access;
- protect against a kernel or container-runtime vulnerability.

The earlier design added GitHub App tokens and changed home and project paths.
Those choices did not help protect the host OS and caused Claude state and plugins
to use the wrong locations.

## 2. Why host paths must be preserved

The container runs as the host user, with the same `$HOME`, and mounts the project
at its host path. Claude therefore writes sessions, memories, plugins, settings,
and refreshed credentials where the host installation expects them. Only the
operating-system files come from the image.

Claude groups project state by working-directory path. The old container mounted
every project at `/workspace`, so it wrote to a separate `-workspace` state
directory instead of the project's normal directory. Plugins also stored absolute
paths below the host home directory, but the old container used `/home/dev`.
Keeping both paths unchanged fixes these problems without translation code.

## 3. Launch process

When started in a Git repository, `ccbox`:

1. finds the repository root;
2. checks that Docker provides `sysbox-runc`;
3. resolves the host `claude` executable;
4. creates a project name for `ccbox-docker-<project>`;
5. combines default ports, `CCBOX_PORTS`, and `<project>/.ccbox/ports`;
6. runs Docker as the host UID and GID, with the host home and project paths;
7. starts inner Docker and then runs
   `claude --dangerously-skip-permissions "$@"`.

## 4. Mounts and settings

| Mount or setting | Access | Purpose |
| --- | --- | --- |
| Project at its host path | read-write | Keep edits, commits, and project state in their normal location |
| `~/.claude` | read-write | Preserve sessions, memories, plugins, settings, and credential refresh |
| `~/.gitconfig` | read-only | Use the host commit identity and Git settings |
| Host Claude installation | read-only | Run the same Claude version as the host |
| `ccbox-docker-<project>` at `/var/lib/docker` | read-write volume | Preserve inner-Docker data for that project |
| `CLAUDE_CONFIG_DIR=$HOME/.claude` | environment | Keep `.claude.json` inside the mounted directory |
| Host username, UID, GID, and home path | environment | Avoid root and preserve absolute paths |
| Published ports | Docker setting | Reach services from the host browser |
| Terminal tint, `CCBOX=1`, `CCBOX_VERSION` | environment | Identify a ccbox session |

The container does not mount `~/.config/gh`, `~/.ssh`, shell startup files, or
other home-directory contents. The user pushes from the host.

### Claude comes from the host

The image does not install Claude Code. The launcher mounts the host native
installation read-only, which keeps host and container versions equal without an
image rebuild. Node.js remains in the image for project work and Node-based
plugins.

The approved design supports the native installer under
`~/.local/share/claude/versions`. An npm-global installation would also require
its Node module tree and was left for later work.

## 5. Removed and retained parts

The redesign removes:

- the GitHub App helper, private key, token creation, and related documentation;
- `/home/dev`, `/workspace`, and all path-conversion links and variables;
- the Claude Code package and version selection from the image.

It keeps:

- sysbox and inner Docker with one data volume per project;
- published ports, terminal tint, and `CCBOX` markers;
- shared language caches;
- the Dockerfile order that keeps toolchain layers cached when only the
  entrypoint changes.

## 6. Files Claude can change

Claude can change the mounted project, all of `~/.claude`, and the project's
inner-Docker data. Git history is the recovery method for project files.

It cannot reach other projects, other home-directory contents, or GitHub
credentials through normal mounted paths. Sysbox maps container root to an
unprivileged host user. The launcher must never mount the host Docker socket or
use `--privileged` or `--network=host`.

Because `~/.claude` is writable, Claude can damage its host settings, plugins,
or credentials. Shared login state may also expose account connectors available
to Claude. These effects were accepted to preserve a complete Claude environment.

## 7. Migration and remaining work

Implementation required:

- one image rebuild for the new user and home path;
- removal of obsolete `~/.config/ccbox` GitHub App files;
- optional removal of the old `-workspace` Claude project directory;
- updates to the launcher, entrypoint, Dockerfile, README, and version.

Deferred items were support for npm-global Claude installations and optional
shell-startup-file mounts.

## Verification completed during design

Tests against the existing image showed:

- all 14 plugins failed when their recorded host paths did not exist in the
  container;
- exposing the same host home path made all 14 plugins load;
- Claude state contained both the correct host project directory and the old
  `-workspace` directory;
- the read-only host Claude binary ran successfully in the container and reported
  the same version (`2.1.185`);
- Git used the host identity from a read-only `.gitconfig`, created commits in the
  writable project, and could not modify the Git configuration.
