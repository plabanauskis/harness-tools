<p>
  <a href="tools/cchat/README.md"><picture><source media="(prefers-color-scheme: dark)" srcset="tools/cchat/assets/icon-dark.svg"><img src="tools/cchat/assets/icon.svg" alt="cchat" height="56"></picture></a>
  &nbsp;&nbsp;&nbsp;
  <a href="tools/ccsession/README.md"><picture><source media="(prefers-color-scheme: dark)" srcset="tools/ccsession/assets/icon-dark.svg"><img src="tools/ccsession/assets/icon.svg" alt="ccsession" height="56"></picture></a>
  &nbsp;&nbsp;&nbsp;
  <a href="tools/ccbox/README.md"><picture><source media="(prefers-color-scheme: dark)" srcset="tools/ccbox/assets/icon-dark.svg"><img src="tools/ccbox/assets/icon.svg" alt="ccbox" height="56"></picture></a>
</p>

# cctools

Three Claude Code terminal tools maintained in the
[harness-tools monorepo](../../README.md).

| Tool | Purpose | Requires | Platform |
| --- | --- | --- | --- |
| <picture><source media="(prefers-color-scheme: dark)" srcset="tools/cchat/assets/icon-dark.svg"><img src="tools/cchat/assets/icon.svg" alt="" width="20" height="20"></picture> [cchat](tools/cchat/README.md) | Start a chat in a temporary directory | `claude` | Linux, macOS |
| <picture><source media="(prefers-color-scheme: dark)" srcset="tools/ccsession/assets/icon-dark.svg"><img src="tools/ccsession/assets/icon.svg" alt="" width="20" height="20"></picture> [ccsession](tools/ccsession/README.md) | Find and resume sessions with fzf | `fzf`, `jq`, `claude`, GNU tools | Linux, macOS |
| <picture><source media="(prefers-color-scheme: dark)" srcset="tools/ccbox/assets/icon-dark.svg"><img src="tools/ccbox/assets/icon.svg" alt="" width="20" height="20"></picture> [ccbox](tools/ccbox/README.md) | Run autonomous Claude Code in a sysbox container | `docker`, `sysbox-runc`, `claude` | Linux amd64 |

## Install

Requires Bash, Git, and curl. No GitHub account or sudo is needed:

```bash
curl -fsSL https://raw.githubusercontent.com/plabanauskis/harness-tools/main/install.sh | bash -s -- --suite=cctools
```

Add `--tools=cchat,ccsession` to select tools directly, `--all` for all three,
or `--force` to keep links when a requirement check fails. From a complete
source checkout, you can instead run `bash suites/cctools/install.sh`.

For a development install from committed local files:

```bash
CCTOOLS_REPO="file://$PWD" bash install.sh --suite=cctools --tools=cchat
```

Set `CCTOOLS_BRANCH` if the desired commit is not on `main`.

## Manage tools

```bash
cctools list
cctools doctor [tool]
cctools enable ccbox
cctools disable cchat
cctools update
cctools version [tool]
cctools uninstall
```

The default install directory is `~/.local/share/cctools`; override it with
`CCTOOLS_HOME`. Commands are linked in `~/.local/bin`; override that with
`CCTOOLS_BIN`. `CCTOOLS_REPO` and `CCTOOLS_BRANCH` select the source for a new
install.

`update` refuses tracked local changes, fetches and prunes its source, then
resets the managed clone. Uninstall removes owned links and the clone, but not
Claude state or Docker data. Follow the [migration guide](../../docs/migration.md) for
an installation from the old separate repository.

## Safety

`ccbox` runs Claude Code with `--dangerously-skip-permissions` in a sysbox
container. The project and Claude state are writable. Read [what ccbox can
change](tools/ccbox/README.md#safety) before using it. `cchat` and `ccsession`
do not add that option.

## Development and releases

From the repository root, run `scripts/check.sh`. Use
`scripts/release.sh <tool> <version>` to prepare a tool release. See the
[root development guide](../../README.md#development-and-releases).

[MIT](LICENSE). ccbox includes work adapted from
[RchGrav/claudebox](https://github.com/RchGrav/claudebox).
