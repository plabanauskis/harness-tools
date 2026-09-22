<p>
  <a href="tools/cochat/README.md"><picture><source media="(prefers-color-scheme: dark)" srcset="tools/cochat/assets/icon-dark.svg"><img src="tools/cochat/assets/icon.svg" alt="cochat" height="56"></picture></a>
  &nbsp;&nbsp;&nbsp;
  <a href="tools/cosession/README.md"><picture><source media="(prefers-color-scheme: dark)" srcset="tools/cosession/assets/icon-dark.svg"><img src="tools/cosession/assets/icon.svg" alt="cosession" height="56"></picture></a>
  &nbsp;&nbsp;&nbsp;
  <a href="tools/cobox/README.md"><picture><source media="(prefers-color-scheme: dark)" srcset="tools/cobox/assets/icon-dark.svg"><img src="tools/cobox/assets/icon.svg" alt="cobox" height="56"></picture></a>
</p>

# cotools

Three Codex terminal tools maintained in the
[harness-tools monorepo](../../README.md).

| Tool | Purpose | Requires | Platform |
| --- | --- | --- | --- |
| <picture><source media="(prefers-color-scheme: dark)" srcset="tools/cochat/assets/icon-dark.svg"><img src="tools/cochat/assets/icon.svg" alt="" width="20" height="20"></picture> [cochat](tools/cochat/README.md) | Start a chat in a temporary directory | `codex` | Linux, macOS |
| <picture><source media="(prefers-color-scheme: dark)" srcset="tools/cosession/assets/icon-dark.svg"><img src="tools/cosession/assets/icon.svg" alt="" width="20" height="20"></picture> [cosession](tools/cosession/README.md) | Find and resume sessions with fzf | `fzf`, `jq`, `codex` | Linux, macOS |
| <picture><source media="(prefers-color-scheme: dark)" srcset="tools/cobox/assets/icon-dark.svg"><img src="tools/cobox/assets/icon.svg" alt="" width="20" height="20"></picture> [cobox](tools/cobox/README.md) | Run autonomous Codex in a sysbox container | `docker`, `sysbox-runc`, `codex` | Linux amd64 |

## Install

Requires Bash, Git, and curl. No GitHub account or sudo is needed:

```bash
curl -fsSL https://raw.githubusercontent.com/plabanauskis/harness-tools/main/install.sh | bash -s -- --suite=cotools
```

Add `--tools=cochat,cosession` to select tools directly, `--all` for all three,
or `--force` to keep links when a requirement check fails. From a complete
source checkout, you can instead run `bash suites/cotools/install.sh`.

For a development install from committed local files:

```bash
COTOOLS_REPO="file://$PWD" bash install.sh --suite=cotools --tools=cochat
```

Set `COTOOLS_BRANCH` if the desired commit is not on `main`.

## Manage tools

```bash
cotools list
cotools doctor [tool]
cotools enable cobox
cotools disable cochat
cotools update
cotools version [tool]
cotools uninstall
```

The default install directory is `~/.local/share/cotools`; override it with
`COTOOLS_HOME`. Commands are linked in `~/.local/bin`; override that with
`COTOOLS_BIN`. `COTOOLS_REPO` and `COTOOLS_BRANCH` select the source for a new
install.

`update` refuses tracked local changes, fetches and prunes its source, then
resets the managed clone. Uninstall removes owned links and the clone, but not
Codex state or Docker data. Follow the [migration guide](../../docs/migration.md) for
an installation from the old separate repository.

## Safety

`cobox` passes `--dangerously-bypass-approvals-and-sandbox` to Codex inside a
sysbox container. The project and Codex state are writable. Read [what cobox can
change](tools/cobox/README.md#safety) before using it. `cochat` and `cosession`
do not add that option.

## Development and releases

From the repository root, run `scripts/check.sh`. Use
`scripts/release.sh <tool> <version>` to prepare a tool release. See the
[root development guide](../../README.md#development-and-releases).

[MIT](LICENSE). cobox includes work adapted from
[RchGrav/claudebox](https://github.com/RchGrav/claudebox). This project is not affiliated with or endorsed by OpenAI.
