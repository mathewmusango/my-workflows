# my-workflows

The **public subset** of my shared CI library — reusable GitHub Actions
workflows that are safe for anyone to read and call.

Nothing in this repo is triggered: every workflow is `on: workflow_call`, so
nothing runs here. Consumers own the trigger and call these by remote ref.

> **Generated repository.** This is published from the private library
> (`mathewmusango/myprojects`) by `scripts/publish_workflows.sh`. Edit the
> private source, then re-publish — never hand-edit here, or the two drift.

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

Or call the whole group with one line — `checks.yml@<sha>` fans out to every
leaf below.

## What's here

| Reusable | Fires when changed | Reported check |
| --- | --- | --- |
| `checks-jsonc.yml` | `*.jsonc` | `jsonc` |
| `checks-js.yml` | `*.js` | `syntax` |
| `checks-shell.yml` | `*.sh`, `.githooks/` | `shellcheck` |
| `checks-yaml.yml` | `*.yml`, `*.yaml` | `syntax`, `actionlint` |
| `checks-python.yml` | `*.py`, `pyproject.toml` | `ruff` |
| `checks-terraform.yml` | `*.tf`, `*.tfvars`, `*.hcl` | `fmt`, `validate`, `lint`, `security` |
| `security-secrets.yml` | always | `gitleaks` |
| `security-deps.yml` | pull requests | `dependency-review` |

`checks.yml` aggregates exactly these seven check leaves.

**Not published here** (they stay in the private library, since no public
repository calls them): `checks-docker.yml`, `checks-markdown.yml`.

## How it behaves

Every `checks-*` reusable is **self-gating**: it carries its own `detect` job
(native `git diff`, no third-party action) and its check jobs SKIP when no
matching file changed — a skipped job reports success, so requiring them never
blocks an unrelated PR. The detect step fails safe: if the base commit can't be
resolved (e.g. the first push of a new branch) it runs the check instead of
crashing.

## Conventions

- **Callable only.** No workflow here has a push/PR trigger; consumers own those.
- **Preconditions live in the reusable**, not the caller — e.g.
  `security-deps.yml` gates itself on `pull_request`, so callers don't repeat it.
- **Consumers pin a full commit SHA**, never a moving tag.
- **Third-party actions are pinned to full commit SHAs**, with the version kept
  as a trailing comment.
- **Grouping is by name, not folder** — GitHub requires reusable workflows at the
  top level of `.github/workflows/` and rejects subdirectories.

## License

MIT — see [LICENSE](LICENSE).
