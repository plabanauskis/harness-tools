# Changelog — ccbox

Notable changes to ccbox. Releases follow [Semantic Versioning](https://semver.org).

## 1.1.0 — 2026-06-26

- Allow use outside a Git repository after warning that edits have no Git undo.
- Ask for confirmation before mounting a non-Git directory; abort by default
  without a controlling terminal.

## 1.0.0 — 2026-06-24

- Add autonomous Claude Code in a sysbox container, with the project and Claude
  state mounted at their host paths and the host Claude installation read-only.
- Move distribution from a standalone `.deb` to `cctools enable ccbox`.
- Add `ccbox uninstall` guidance and read the displayed version from
  `CCBOX_VERSION` in `bin/ccbox`.
