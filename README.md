# my-workflows

Reusable GitHub Actions workflows — check and security jobs that other repositories call instead of reimplementing them.

Nothing here is triggered: every workflow is `on: workflow_call`, so nothing runs on pushes to this repo — consumers own the trigger and call these by remote ref. Dependabot (`.github/dependabot.yml`) is the only thing that acts here: it opens the weekly action bumps for the pins below.

## What's available

| Workflow | Checks | Reports |
| --- | --- | --- |
| `checks-jsonc.yml` | `*.jsonc` | `jsonc` |
| `checks-js.yml` | `*.js` | `syntax` |
| `checks-shell.yml` | `*.sh`, `.githooks/` | `shellcheck` |
| `checks-yaml.yml` | `*.yml`, `*.yaml` | `syntax`, `actionlint` |
| `checks-python.yml` | `*.py`, `pyproject.toml` | `ruff` |
| `checks-docker.yml` | `Dockerfile`, compose files | `hadolint` |
| `checks-terraform.yml` | `*.tf`, `*.tfvars`, `*.hcl` | `fmt`, `validate`, `lint` |
| `security-gitleaks.yml` | every run | `gitleaks` |
| `security-gitguardian.yml` | pull requests | `gitguardian` — needs a `GITGUARDIAN_API_KEY` secret |
| `security-terraform.yml` | `*.tf`, `*.tfvars`, `*.hcl` | `security` — the Checkov scan |
| `security-deps.yml` | pull requests | `dependency-review` |
| `checks-links.yml` | scheduled / manual | `links` |
| `cloudfront-invalidate.yml` · `cloudfront-switch.yml` | manual dispatch | — |

`checks.yml` and `security.yml` are group entry points, each naming several of the leaves above in one job. The rest — `checks-docker`, `checks-links` and the two `cloudfront-*` — are called directly, so a repository never inherits a check with nothing to run it against.

Each workflow skips — reporting success — when none of its files changed, so requiring them never blocks an unrelated pull request.

## Usage

```yaml
jobs:
  python:
    uses: mathewmusango/my-workflows/.github/workflows/checks-python.yml@<full-commit-sha> # v<tag>
```

Pin the ref to a **full commit SHA**, never a tag — and keep the release tag in the trailing comment, which is what lets Dependabot version-map the bump.

## Branch protection

`main` is governed by a ruleset — pull requests only, one approval, squash only, signed commits, no bypass — a second governs every branch's *name*, and a third makes the release tags immutable. All three are recorded in [`rulesets/`](rulesets/README.md). One consequence for maintainers: a republish arrives through a pull request rather than a direct push to `main`.

## Security

Report vulnerabilities privately — see [SECURITY.md](SECURITY.md).

## License

MIT — see [LICENSE](LICENSE).
