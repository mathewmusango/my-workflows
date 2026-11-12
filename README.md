# my-workflows

[![Semgrep](https://github.com/mathewmusango/my-workflows/actions/workflows/semgrep.yml/badge.svg?branch=main)](https://github.com/mathewmusango/my-workflows/actions/workflows/semgrep.yml)
[![release](https://img.shields.io/github/v/tag/mathewmusango/my-workflows?label=release&color=blue)](https://github.com/mathewmusango/my-workflows/tags)
[![license](https://img.shields.io/github/license/mathewmusango/my-workflows?color=blue)](LICENSE)
[![last commit](https://img.shields.io/github/last-commit/mathewmusango/my-workflows)](https://github.com/mathewmusango/my-workflows/commits/main)
[![interface](https://img.shields.io/badge/interface-reusable%20workflows-informational)](https://docs.github.com/en/actions/how-tos/reuse-automations/reuse-workflows)

Reusable GitHub Actions workflows — checks and security jobs that other repositories call instead of reimplementing them.

Consumers own the trigger: the leaves are all [`on: workflow_call`](https://docs.github.com/en/actions/how-tos/reuse-automations/reuse-workflows) and are called by remote ref. [Dependabot](.github/dependabot.yml) opens the weekly action bumps for the pins below, and `semgrep.yml` is the one thing that runs on this repository's own pushes and pull requests.

## What's available

| Workflow | Checks | Reports |
| --- | --- | --- |
| `checks-jsonc.yml` | `*.jsonc` | `jsonc` |
| `checks-js.yml` | `*.js` | `syntax` |
| `checks-shell.yml` | `*.sh`, `.githooks/` | `shellcheck` |
| `checks-yaml.yml` | `*.yml`, `*.yaml` | `syntax`, `actionlint` |
| `checks-python.yml` | `*.py`, `pyproject.toml` | `ruff` |
| `checks-docker.yml` | `Dockerfile`/`Containerfile`, compose files | `hadolint`, compose `config` |
| `checks-terraform.yml` | `*.tf`, `*.tfvars`, `*.hcl` | `fmt`, `validate`, `lint` |
| `security-terraform.yml` | every run | `security` — the Checkov scan |
| `security-pip-audit.yml` | every run | `audit` — `pip-audit` over `requirements*.txt` |
| `security-npm-audit.yml` | every run | `audit` — `npm audit` over `package-lock.json` |
| `security-deps.yml` | pull requests | `dependency-review` |
| `security-image.yml` | a given `image-ref` | `image` — Trivy, HIGH/CRITICAL |
| `tests-vitest.yml` | a given `working-directory` | `vitest` (typecheck optional) |
| `release-image-ghcr.yml` | a tag | builds, scans, then pushes to GHCR |
| `release-github.yml` | a tag | the GitHub Release |
| `deploy-links.yml` | scheduled / manual, `site-url` | `links` — a deployed site's sitemap |

`semgrep.yml` is not in that table on purpose: it is a caller, not a leaf. It scans **this** repository on every push to `main`, on pull requests and on a daily schedule, and consumers should not call it — name your own copy, or let the Semgrep app scan your repository.

The `checks-*` workflows skip — reporting success — when none of their files changed, so requiring them never blocks an unrelated pull request. The corpus-driven security leaves (`security-terraform`, `security-pip-audit`, `security-npm-audit`) run **unconditionally**: their verdict comes from an advisory or policy corpus that moves without the tree, so a diff gate would suppress them exactly where they cannot change the answer. `security-deps` is pull-request-only by nature — its input is the diff.

## Usage

```yaml
jobs:
  python:
    uses: mathewmusango/my-workflows/.github/workflows/checks-python.yml@<full-commit-sha> # v<tag>
```

Pin the ref to a **full commit SHA**, never a tag — and keep the release tag in the trailing comment, which is what lets Dependabot version-map the bump.

## Branch protection

Three [rulesets](rulesets/README.md) govern the repository.

## Security

Report vulnerabilities privately — see [SECURITY.md](SECURITY.md).

## License

MIT — see [LICENSE](LICENSE).
