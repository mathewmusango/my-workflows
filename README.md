# my-workflows

Shared **reusable GitHub Actions workflows** — a callable-only library of check
and security workflows, consumed by my other repositories.

Nothing in this repo is triggered: every workflow is `on: workflow_call`, so
nothing runs here. Consumers own the trigger and call these by remote ref.

## Usage

Pin by **full commit SHA** (immutable — a moved tag can't change what runs):

```yaml
name: checks
on:
  pull_request:
    branches: [main]
  workflow_dispatch: {}

permissions:
  contents: read

jobs:
  js:
    uses: mathewmusango/my-workflows/.github/workflows/checks-js.yml@<sha>
  python:
    uses: mathewmusango/my-workflows/.github/workflows/checks-python.yml@<sha>
  shell:
    uses: mathewmusango/my-workflows/.github/workflows/checks-shell.yml@<sha>
  terraform:
    uses: mathewmusango/my-workflows/.github/workflows/checks-terraform.yml@<sha>
  yaml:
    uses: mathewmusango/my-workflows/.github/workflows/checks-yaml.yml@<sha>
  secrets:
    uses: mathewmusango/my-workflows/.github/workflows/security-secrets.yml@<sha>
  deps:
    uses: mathewmusango/my-workflows/.github/workflows/security-deps.yml@<sha>
```

Or call a whole group with one line — `checks.yml` fans out to its leaves.

## Grouping

Grouping is by **name**, not folder — GitHub requires reusable workflows at the
**top level** of `.github/workflows/` and rejects subdirectories. So
`<group>-<surface>.yml` is a leaf and `<group>.yml` aggregates a group.

| Reusable | Fires when changed | Reported check |
| --- | --- | --- |
| `checks-jsonc.yml` | `*.jsonc` | `jsonc` |
| `checks-js.yml` | `*.js` | `syntax` |
| `checks-shell.yml` | `*.sh`, `.githooks/` | `shellcheck` |
| `checks-yaml.yml` | `*.yml`, `*.yaml` | `syntax`, `actionlint` |
| `checks-python.yml` | `*.py`, `pyproject.toml` | `ruff` |
| `checks-docker.yml` | `Dockerfile*`, `*compose.y*ml` | `hadolint` |
| `checks-terraform.yml` | `*.tf`, `*.tfvars`, `*.hcl` | `fmt`, `validate`, `lint`, `security` |
| `checks-markdown.yml` | `*.md` | `markdownlint` |
| `security-secrets.yml` | always | `gitleaks` |
| `security-deps.yml` | pull requests | `dependency-review` |

Every `checks-*` reusable is **self-gating**: it carries its own `detect` job
(native `git diff`, no third-party action) and its check jobs SKIP when no
matching file changed — a skipped job reports success, so requiring them never
blocks an unrelated PR. The detect step fails safe: if the base commit can't be
resolved (e.g. the first push of a new branch) it runs the check instead of
crashing.

`checks-markdown.yml` is **opt-in** — Markdown linting should be adopted per
repository once its tree is clean.

## Conventions

- **Callable only.** No workflow here has a push/PR trigger; consumers own those.
- **Preconditions live in the reusable**, not the caller — e.g.
  `security-deps.yml` gates itself on `pull_request`, so callers don't repeat it.
- **Consumers pin a full commit SHA**, never a moving tag.
- **Third-party actions are pinned to full commit SHAs** with the version kept
  as a trailing comment.

## License

MIT — see [LICENSE](LICENSE).
