# my-workflows

Reusable GitHub Actions workflows — check and security jobs that other
repositories call instead of reimplementing them.

Nothing here is triggered: every workflow is `on: workflow_call`, so nothing runs
on pushes to this repo. Consumers own the trigger and call these by remote ref.

## What's available

| Workflow | Checks | Reports |
| --- | --- | --- |
| `checks-jsonc.yml` | `*.jsonc` | `jsonc` |
| `checks-js.yml` | `*.js` | `syntax` |
| `checks-shell.yml` | `*.sh`, `.githooks/` | `shellcheck` |
| `checks-yaml.yml` | `*.yml`, `*.yaml` | `syntax`, `actionlint` |
| `checks-python.yml` | `*.py`, `pyproject.toml` | `ruff` |
| `checks-terraform.yml` | `*.tf`, `*.tfvars`, `*.hcl` | `fmt`, `validate`, `lint`, `security` |
| `security-secrets.yml` | every run | `gitleaks` |
| `security-deps.yml` | pull requests | `dependency-review` |

Each workflow skips — reporting success — when none of its files changed, so
requiring them never blocks an unrelated pull request.

## Usage

```yaml
jobs:
  python:
    uses: mathewmusango/my-workflows/.github/workflows/checks-python.yml@<full-commit-sha>
```

Pin the ref to a **full commit SHA**. Call `checks.yml` the same way to get every
check above in a single job.

## License

MIT — see [LICENSE](LICENSE).
