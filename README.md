# my-workflows

Reusable GitHub Actions workflows — checks and security jobs that other repositories call instead of reimplementing them.

Nothing here is triggered: every workflow is [`on: workflow_call`](https://docs.github.com/en/actions/how-tos/reuse-automations/reuse-workflows), so nothing runs on pushes to this repo — consumers own the trigger and call these by remote ref. [Dependabot](.github/dependabot.yml) is the only thing that acts here: it opens the weekly action bumps for the pins below.

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
| `security-terraform.yml` | every run | `security` — the Checkov scan |
| `security-pip-audit.yml` | every run | `audit` — `pip-audit` over `requirements*.txt` |
| `security-npm-audit.yml` | every run | `audit` — `npm audit` over `package-lock.json` |
| `security-deps.yml` | pull requests | `dependency-review` |
| `checks-links.yml` | scheduled / manual | `links` |

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
