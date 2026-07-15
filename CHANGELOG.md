# Changelog

## v0.1.3 — 2026-07-15

### Fixed

- `deploy` role `argument_specs.yml`: changed `name` from `required: true` to
  `required: false` in `elise_quadlet_pod.options` and `elise_quadlet_network.options`.
  Ansible 2.16's `validate_argument_spec` enforces required sub-options even when the
  parent dict is empty (`{}`), causing validation failure when callers pass
  `elise_quadlet_pod: {}` or `elise_quadlet_network: {}` to indicate no pod/network
  is needed. The description already stated these should be omittable; the arg spec
  now matches that intent.

## v0.1.2 — 2026-06-11

### Added

- `deploy` role `container.j2`: `network_mode` field for raw `Network=` values (e.g. `host`).
  Takes precedence over `network` when both are set. Required for containers that need host
  networking (e.g. DHCP servers receiving L2 broadcast traffic).
- `deploy` role `container.j2`: `capabilities` list for `AddCapability=` directives.
  Use for containers requiring elevated Linux capabilities (e.g. `NET_ADMIN`, `NET_RAW`
  for DHCP server functionality).
- `deploy` role `argument_specs.yml`: documented `network_mode` and `capabilities` fields
  on container entries.

## v0.1.1 — 2026-06-10

### Added

- `deploy` role `container.j2`: `env:` dict support for inline `Environment=` Quadlet
  directives. Use for per-container non-secret env vars that differ across containers
  (e.g. `POSTGRESQL_USER`). Secrets should continue to use `EnvironmentFile`.
- `deploy` role `argument_specs.yml`: documented `env` field on container entries.

## v0.1.0 — 2026-06-10

### Added

- Initial collection scaffold
- `deploy` role — prerequisites, image pull, and Quadlet unit rendering (container, pod, network, volume, env)
- `start` role — health-gated tier startup for pull mode
- `yield` role — disable and stop services while preserving all state
- `reclaim` role — re-enable and start services from preserved state
- `validate` role — inline container health assertion (pull mode) or deferred stub (embedded mode)
- CI: ansible-lint and ansible-test sanity workflows
