# Changelog

## v0.1.0 — 2026-06-10

### Added

- Initial collection scaffold
- `deploy` role — prerequisites, image pull, and Quadlet unit rendering (container, pod, network, volume, env)
- `start` role — health-gated tier startup for pull mode
- `yield` role — disable and stop services while preserving all state
- `reclaim` role — re-enable and start services from preserved state
- `validate` role — inline container health assertion (pull mode) or deferred stub (embedded mode)
- CI: ansible-lint and ansible-test sanity workflows
