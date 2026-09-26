# Changelog

All notable changes to the published subset are documented here.

Releases are **timestamp tags** — `v<year>.<MMDD>.<HHMM>Z`, stamped in UTC when the release is cut and never moved. Consumers pin a full commit SHA with the tag in a trailing comment, so a tag labels a published commit rather than tracking one. Newest first.

## [2026.0926.0023Z] - 2026-09-26

### Removed

- `checks-markdown.yml` — Markdown linting is dropped, and no repository calls the leaf.

## [2026.0925.1807Z] - 2026-09-25

### Removed

- `branch-policy.yml` — branch naming is a ruleset now, and a `create:`-triggered workflow could only report after the name was already made.

### Changed

- `CONTRIBUTING.md` and `CODE_OF_CONDUCT.md` join the published non-workflow files.

## [2026.0925.1503Z] - 2026-09-25

### Fixed

- `security-gitleaks.yml`: the pull-request arm passes its log options as a single argument, which is what the scanner accepts.

## [2026.0925.1427Z] - 2026-09-25

### Added

- `security.yml` — a group entry point over the security leaves, so a caller names one job instead of three.
- `security-terraform.yml` — the Checkov scan, split out of `checks-terraform.yml`.

### Changed

- `checks.yml` regenerated over the current leaves; `checks-docker.yml` and `checks-terraform.yml` updated.

*First tag carrying the UTC `Z` suffix.*

## [2026.0925.1228] - 2026-09-25

### Added

- `security-gitleaks.yml` and `security-gitguardian.yml`.
- `branch-policy.yml`.

### Removed

- `security-secrets.yml`, replaced by `security-gitleaks.yml`.

### Changed

- `checks.yml` regenerated.

## [2026.0922.1933] - 2026-09-22

### Added

- `checks-docker.yml`.

### Changed

- `checks-shell.yml`, `checks-terraform.yml`, `.github/dependabot.yml`, `SECURITY.md`.

## [2026.0917.2243] - 2026-09-17

### Added

- `cloudfront-invalidate.yml` and `cloudfront-switch.yml`.

## [2026.0916.2112] - 2026-09-16

### Added

- `checks-markdown.yml`.

### Changed

- `.github/dependabot.yml`.

## [2026.0916.0520] - 2026-09-16

### Changed

- `security-deps.yml`: the per-workflow licence allowlist gave way to `license-check: false`.

---

Earlier history predates this file. The subset was published from the library's start and has been tagged from `v2026.0916.0520` onwards.
