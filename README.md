# harness-tools

Small terminal tools for **Claude Code, Codex, and Pi**. Each harness has an
independently installable suite; all three share installation and maintenance code.

| Purpose | Claude Code | Codex | Pi |
| :--- | :---: | :---: | :---: |
| Temporary chat outside a project | <picture><source media="(prefers-color-scheme: dark)" srcset="suites/cctools/tools/cchat/assets/icon-dark.svg"><img src="suites/cctools/tools/cchat/assets/icon.svg" alt="" width="32" height="32"></picture><br>[cchat](suites/cctools/tools/cchat/README.md) | <picture><source media="(prefers-color-scheme: dark)" srcset="suites/cotools/tools/cochat/assets/icon-dark.svg"><img src="suites/cotools/tools/cochat/assets/icon.svg" alt="" width="32" height="32"></picture><br>[cochat](suites/cotools/tools/cochat/README.md) | <picture><source media="(prefers-color-scheme: dark)" srcset="suites/pitools/tools/pichat/assets/icon-dark.svg"><img src="suites/pitools/tools/pichat/assets/icon.svg" alt="" width="32" height="32"></picture><br>[pichat](suites/pitools/tools/pichat/README.md) |
| Find and resume sessions with fzf | <picture><source media="(prefers-color-scheme: dark)" srcset="suites/cctools/tools/ccsession/assets/icon-dark.svg"><img src="suites/cctools/tools/ccsession/assets/icon.svg" alt="" width="32" height="32"></picture><br>[ccsession](suites/cctools/tools/ccsession/README.md) | <picture><source media="(prefers-color-scheme: dark)" srcset="suites/cotools/tools/cosession/assets/icon-dark.svg"><img src="suites/cotools/tools/cosession/assets/icon.svg" alt="" width="32" height="32"></picture><br>[cosession](suites/cotools/tools/cosession/README.md) | <picture><source media="(prefers-color-scheme: dark)" srcset="suites/pitools/tools/pisession/assets/icon-dark.svg"><img src="suites/pitools/tools/pisession/assets/icon.svg" alt="" width="32" height="32"></picture><br>[pisession](suites/pitools/tools/pisession/README.md) |
| Autonomous work in an isolated container | <picture><source media="(prefers-color-scheme: dark)" srcset="suites/cctools/tools/ccbox/assets/icon-dark.svg"><img src="suites/cctools/tools/ccbox/assets/icon.svg" alt="" width="32" height="32"></picture><br>[ccbox](suites/cctools/tools/ccbox/README.md) | <picture><source media="(prefers-color-scheme: dark)" srcset="suites/cotools/tools/cobox/assets/icon-dark.svg"><img src="suites/cotools/tools/cobox/assets/icon.svg" alt="" width="32" height="32"></picture><br>[cobox](suites/cotools/tools/cobox/README.md) | <picture><source media="(prefers-color-scheme: dark)" srcset="suites/pitools/tools/pibox/assets/icon-dark.svg"><img src="suites/pitools/tools/pibox/assets/icon.svg" alt="" width="32" height="32"></picture><br>[pibox](suites/pitools/tools/pibox/README.md) |
| Manage installed tools | [cctools](suites/cctools/README.md) | [cotools](suites/cotools/README.md) | [pitools](suites/pitools/README.md) |

Chat tools and the Codex/Pi session pickers support Linux and macOS. `ccsession`
also requires GNU `find`, `grep`, `date`, and `stat`. Box tools require Linux
amd64, Docker, sysbox, and a locally built image. Installing another tool never
builds that image.

## Install

Requires **Bash, Git, and curl**. No GitHub account or sudo is needed. Choose one suite:

```bash
# Claude Code
curl -fsSL https://raw.githubusercontent.com/plabanauskis/harness-tools/main/install.sh | bash -s -- --suite=cctools

# Codex
curl -fsSL https://raw.githubusercontent.com/plabanauskis/harness-tools/main/install.sh | bash -s -- --suite=cotools

# Pi
curl -fsSL https://raw.githubusercontent.com/plabanauskis/harness-tools/main/install.sh | bash -s -- --suite=pitools
```

The installer shows a tool picker when a terminal is available. Use
`--tools=pichat,pisession` to select tools directly or `--all` for the entire
suite. `--force` keeps requested command links even when a dependency or platform
check fails.

To inspect the installer before running it:

```bash
curl -fsSL -o install.sh https://raw.githubusercontent.com/plabanauskis/harness-tools/main/install.sh
less install.sh
bash install.sh --suite=pitools --tools=pichat,pisession
```

For development, install committed files from a local clone:

```bash
git clone https://github.com/plabanauskis/harness-tools.git
cd harness-tools
PITOOLS_REPO="file://$PWD" bash install.sh --suite=pitools --tools=pichat
```

Set `PITOOLS_BRANCH` when using a branch other than `main`. Replace the `PI`
prefix for the other suites. The local path must remain available for updates.

| Suite | Install directory | Bin directory | Source settings |
| --- | --- | --- | --- |
| cctools | `CCTOOLS_HOME` | `CCTOOLS_BIN` | `CCTOOLS_REPO`, `CCTOOLS_BRANCH` |
| cotools | `COTOOLS_HOME` | `COTOOLS_BIN` | `COTOOLS_REPO`, `COTOOLS_BRANCH` |
| pitools | `PITOOLS_HOME` | `PITOOLS_BIN` | `PITOOLS_REPO`, `PITOOLS_BRANCH` |

Defaults are `~/.local/share/<suite>`, `~/.local/bin`, this repository, and
`main`. Each install directory contains a full repository clone and the legacy
`.agent-tools-suite` ownership marker. Keep suite install directories separate
and keep custom directory settings in your shell environment.

Existing installation from an older repository? Read the [migration guide](docs/migration.md).

## Manage tools

```bash
pitools list
pitools doctor
pitools enable pibox       # links the command; does not build the image
pitools disable pichat
pitools update
pitools version
pitools uninstall
```

Use `cctools` or `cotools` for the other suites. `update` refuses tracked local
changes, then resets the managed clone to its configured source. Do not develop
inside an installed clone.

Suite uninstall removes only owned links and the managed clone. It does not
remove sessions, credentials, projects, Docker images, or volumes. Use the box
command's own `uninstall` subcommand to remove its Docker data.

## Repository layout

```text
bin/                  shared manager and suite command links
lib/                  shared installer, manager, and release code
scripts/              checks, development setup, and releases
suites/<suite>/        suite entrypoints, documentation, and tests
  tools/<tool>/        tool scripts, manifests, versions, assets, and docs
docker/               shared Codex/Pi Dockerfile
tests/                shared installation and management tests
```

Harness-specific parsers, launch arguments, authentication, mounts, and stored
data remain separate. Codex and Pi share a Dockerfile but use separate images,
entrypoints, credentials, and volumes. Claude uses its own image.

Read a box tool's safety section before use. Its project and harness-state mounts
are writable, and containers do not protect against every possible harmful action.

## Development and releases

```bash
scripts/check.sh
scripts/dev-setup.sh      # optional pre-push check hook
AGENT_TOOLS_SMOKE=1 scripts/check.sh
```

Checks require Bash, Git, jq, shellcheck, shfmt, ripgrep, ImageMagick, and
xmllint. Debian and Ubuntu provide the last two through `imagemagick` and
`libxml2-utils`. Live Docker tests run only when `AGENT_TOOLS_SMOKE=1`; checks
never build an image automatically.

Release one tool with `scripts/release.sh <tool> <version>`. The script requires
a clean tree, updates version files and the changelog, commits, and creates a
`<tool>-vX.Y.Z` tag. Replace the generated changelog placeholder before publishing.
The optional `--gh` flag pushes and creates a draft GitHub release.

## History and license

The three original repositories were imported with their histories intact. See
[migration and provenance](docs/migration.md) for import commits and rename details.

[MIT](LICENSE). Box code includes work adapted from
[RchGrav/claudebox](https://github.com/RchGrav/claudebox). This project is
independent of the harness vendors and maintainers.
