# Changelog — `armature-discovery`

All notable changes to this crate will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this crate adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

Earlier changes are recorded in the workspace [`CHANGELOG.md`](../CHANGELOG.md).

## [Unreleased]

## [0.4.0] - 2026-10-17

### Security

- `ConsulDiscovery::with_acl_token` sends a Consul ACL token
  (`X-Consul-Token`, marked sensitive) on every request (M41).
- `EtcdDiscovery::with_credentials` authenticates against etcd v3 auth
  (`/v3/auth/authenticate`), sends the token on every request including lease
  keep-alives, and re-authenticates once on a 401. `Debug` of the credentials
  is redacted (M41).
- etcd service names containing `/` or control characters (or empty) are
  rejected: `discover("api/admin")` used to range-scan another service's
  sub-tree, and `""` widened the scan to every service. Service ids are
  validated the same way (M41).

### Changed

- **Breaking:** `EtcdDiscovery::{register, discover, get_service, deregister}`
  return `DiscoveryError::InvalidConfiguration` for such names/ids. Migration:
  use names without `/`.

## [0.3.2] - 2026-09-15

### Changed

- Dependencies bumped to their latest releases: `base64` 0.22 → 0.23, `tokio` 1.52 → 1.53.

## [0.3.1] - 2026-08-04

### Fixed

- Requirements on sibling armature crates name a minor instead of `0`. Under
  Cargo's 0.x rules `version = "0"` matches any release ever made, and edition
  2024 selects the MSRV-aware resolver, so a consumer declaring an older
  `rust-version` was handed the oldest version satisfying it — resolving
  `armature-core = "0"` on Rust 1.89 produced `armature-core 0.2.3` while an
  explicit `armature-core = "0.8"` elsewhere in the same graph pulled 0.8.2.
  Two copies of core, and a build failing on symbols the older one lacks. Each
  0.x minor in this family is a breaking change, so the requirement now names
  one. No API change.
