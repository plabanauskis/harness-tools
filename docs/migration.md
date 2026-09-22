# Migration and repository history

## What changed

Development now happens in `plabanauskis/harness-tools`. The original `cctools`,
`cotools`, and `pitools` repositories and local source checkouts were not changed
or archived. Existing installed clones continue to use their original source
until you replace them.

The repository is public, so installation and updates do not require a GitHub
account. See the [installation guide](../README.md#install).

The same monorepo was previously named `plabanauskis/agent-tools`. Existing
installs from that monorepo do not need to be reinstalled; update their Git URL
as described below. The separate `agentic-tools` repository is unrelated.

## Repository rename

GitHub preserved commits, tags, issues, releases, and redirects when
`agent-tools` became `harness-tools`. Updating each clone avoids relying on the
redirect. For a Pi installation:

```bash
prefix="${PITOOLS_HOME:-$HOME/.local/share/pitools}"
git -C "$prefix" remote get-url origin
git -C "$prefix" remote set-url origin https://github.com/plabanauskis/harness-tools.git
```

Make the same change for another suite only when its origin is the old monorepo.
Do not replace an intentional fork or an original suite repository. Also update
saved installer commands and `*_REPO` settings.

A development checkout may be renamed locally. Update its Git origin afterward.
If an installed clone uses a `file://` URL, change that URL to the checkout's new
absolute path. Recreate any command links that point directly into a moved
checkout.

Do not rename suite directories, commands, environment settings, Docker images
or volumes, or the `.agent-tools-suite` ownership marker. The marker keeps its
old name for compatibility. No Docker rebuild or state migration is needed.

## Move an installation from an original suite repository

These steps replace an **installed clone**, normally
`~/.local/share/<suite>`. They do not replace a development checkout. Do not run
a box tool's uninstall command unless you also want to remove its Docker data.

The example uses Pi; substitute the Claude Code or Codex suite names and settings
when needed.

1. Record the current settings and enabled tools:

   ```bash
   pitools list
   printf 'prefix: %s\nbin: %s\n' \
     "${PITOOLS_HOME:-$HOME/.local/share/pitools}" \
     "${PITOOLS_BIN:-$HOME/.local/bin}"
   ```

   Save the enabled-tool list. Back up any local changes in the installed clone.
   Record custom `PITOOLS_REPO` and `PITOOLS_BRANCH` settings, but do not reuse an
   old repository URL by accident.

2. Get the monorepo:

   ```bash
   git clone https://github.com/plabanauskis/harness-tools.git
   cd harness-tools
   ```

   Reuse an existing checkout when available.

3. Move the old installed clone aside:

   ```bash
   prefix="${PITOOLS_HOME:-$HOME/.local/share/pitools}"
   backup="${prefix}.pre-harness-tools"
   test -d "$prefix/.git" && test ! -e "$backup" && mv -- "$prefix" "$backup"
   ```

   Stop if the move fails. Command links may remain broken until installation
   finishes. This backup contains the old clone, not Pi sessions or credentials.

4. Install every previously enabled tool:

   ```bash
   # Example selection; use the list saved in step 1.
   PITOOLS_REPO=https://github.com/plabanauskis/harness-tools.git PITOOLS_BRANCH=main \
     bash install.sh --suite=pitools --tools=pichat,pisession
   ```

   Keep custom `PITOOLS_HOME` and `PITOOLS_BIN` values exported. Include `pibox`
   if it was enabled. `--force` can preserve requested links while a dependency
   is temporarily unavailable.

5. Verify the new installation:

   ```bash
   pitools list
   pitools doctor
   pitools version
   readlink "${PITOOLS_BIN:-$HOME/.local/bin}/pichat"
   ```

   Tool links should point below `<prefix>/suites/pitools/tools/`; the manager
   link should point to `<prefix>/bin/pitools`. Remove a stale old tool link only
   after confirming that it is owned by the old installation.

Repeat the process separately for `cctools` and `cotools`. Never give two suites
the same install directory.

### State and rollback

The migration does not change sessions, credentials, project settings, image
names, or volume names. It does not require a Docker rebuild.

To roll back, move the new clone elsewhere, restore the old clone at its original
path, and use the old manager's `enable` commands to restore tool links. Restore
the manager link to `<prefix>/bin/<suite>` as well. Do not run the new manager's
uninstall command after restoring the old clone because the old clone does not
carry the monorepo ownership marker.

## Imported history

The original histories remain reachable from `main` through merge commits. Their
commit IDs were not rewritten.

| Source | Imported source HEAD | Import merge |
| --- | --- | --- |
| cctools | `84c9773b9935d7f40cc61a7ac8b8e9c389012761` | `b62f14c` |
| cotools | `f64adc500e2e7ab042b6e0579fe0ef3041737925` | `d738e86` |
| pitools | `f2f32b810222b4324d0a48d6e6ad36b4bb6bd76e` | `c98e38c` |

The retained `ccbox-v1.1.0` tag points to the original single-suite layout. New
tool tags identify complete monorepo revisions. To inspect a file before import,
use `git show <source-commit>:<original-path>`; those paths do not include the
new `suites/<suite>/` prefix.

Licenses and tool changelogs remain under each suite. Before the initial public
release, Gitleaks found no secrets in reachable history or current tracked files.
Repeat both scans before publishing another imported history. A clean scan cannot
prove that every sensitive value was found.
