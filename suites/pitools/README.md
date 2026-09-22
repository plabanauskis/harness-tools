<p>
  <a href="tools/pichat/README.md"><picture><source media="(prefers-color-scheme: dark)" srcset="tools/pichat/assets/icon-dark.svg"><img src="tools/pichat/assets/icon.svg" alt="pichat" height="56"></picture></a>
  &nbsp;&nbsp;&nbsp;
  <a href="tools/pisession/README.md"><picture><source media="(prefers-color-scheme: dark)" srcset="tools/pisession/assets/icon-dark.svg"><img src="tools/pisession/assets/icon.svg" alt="pisession" height="56"></picture></a>
  &nbsp;&nbsp;&nbsp;
  <a href="tools/pibox/README.md"><picture><source media="(prefers-color-scheme: dark)" srcset="tools/pibox/assets/icon-dark.svg"><img src="tools/pibox/assets/icon.svg" alt="pibox" height="56"></picture></a>
</p>

# pitools

Three Pi terminal tools maintained in the
[harness-tools monorepo](../../README.md).

| Tool | Purpose | Requires | Platform |
| --- | --- | --- | --- |
| <picture><source media="(prefers-color-scheme: dark)" srcset="tools/pichat/assets/icon-dark.svg"><img src="tools/pichat/assets/icon.svg" alt="" width="20" height="20"></picture> [pichat](tools/pichat/README.md) | Start a chat in a temporary directory | `pi` | Linux, macOS |
| <picture><source media="(prefers-color-scheme: dark)" srcset="tools/pisession/assets/icon-dark.svg"><img src="tools/pisession/assets/icon.svg" alt="" width="20" height="20"></picture> [pisession](tools/pisession/README.md) | Find and resume sessions with fzf | `fzf`, `jq`, `pi` | Linux, macOS |
| <picture><source media="(prefers-color-scheme: dark)" srcset="tools/pibox/assets/icon-dark.svg"><img src="tools/pibox/assets/icon.svg" alt="" width="20" height="20"></picture> [pibox](tools/pibox/README.md) | Run Pi in a sysbox container | `docker`, `sysbox-runc`, `pi` | Linux amd64 |

## Install

Requires Bash, Git, and curl. No GitHub account or sudo is needed:

```bash
curl -fsSL https://raw.githubusercontent.com/plabanauskis/harness-tools/main/install.sh | bash -s -- --suite=pitools
```

Add `--tools=pichat,pisession` to select tools directly, `--all` for all three,
or `--force` to keep links when a requirement check fails. From a complete
source checkout, you can instead run `bash suites/pitools/install.sh`.

For a development install from committed local files:

```bash
PITOOLS_REPO="file://$PWD" bash install.sh --suite=pitools --tools=pichat
```

Set `PITOOLS_BRANCH` if the desired commit is not on `main`.

## Manage tools

```bash
pitools list
pitools doctor [tool]
pitools enable pibox
pitools disable pichat
pitools update
pitools version [tool]
pitools uninstall
```

The default install directory is `~/.local/share/pitools`; override it with
`PITOOLS_HOME`. Commands are linked in `~/.local/bin`; override that with
`PITOOLS_BIN`. `PITOOLS_REPO` and `PITOOLS_BRANCH` select the source for a new
install.

`update` refuses tracked local changes, fetches and prunes its source, then
resets the managed clone. Uninstall removes owned links and the clone, but not
Pi state or Docker data. Follow the [migration guide](../../docs/migration.md) for an
installation from the old separate repository.

## Safety

Pi already runs tools with full permissions; pibox does not add a bypass option.
It runs the whole Pi process in a sysbox container. The project and all host
state under `$HOME/.pi` are writable. Custom `PI_CODING_AGENT_DIR` and
`PI_CODING_AGENT_SESSION_DIR` paths outside that directory are also mounted when
configured. Read [what pibox can change](tools/pibox/README.md#safety) before use.

## Development and releases

From the repository root, run `scripts/check.sh`. Use
`scripts/release.sh <tool> <version>` to prepare a tool release. See the
[root development guide](../../README.md#development-and-releases).

[MIT](LICENSE). pibox includes work adapted from
[RchGrav/claudebox](https://github.com/RchGrav/claudebox). This project is
independent of the Pi maintainers and Earendil Works.
