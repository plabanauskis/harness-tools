# Changelog — pibox

Notable changes to pibox. Releases follow [Semantic Versioning](https://semver.org).

## 1.0.0 — 2026-08-28

- Add a sysbox container for Pi's full-permission tool model.
- Mount npm and compiled Pi installations read-only at their host paths.
- Preserve the project, Pi state, custom session paths, caches, and inner-Docker
  data at their host paths.
- Add approved provider credentials, doctor, build, uninstall, ports, and
  terminal tint.
