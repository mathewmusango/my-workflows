# Ruleset: `main` — record

**Status:** 🟢 applied — live on `refs/heads/main` · **Config:** [`main.json`](main.json)

**Purpose.** `main` binds every actor: pull requests only, one approval, squash only, signed commits, no bypass.

| Field | Value |
| --- | --- |
| Enforcement | `active` |
| Merge methods | `squash` only |
| Approvals | 1 · stale reviews dismissed on push · review threads resolved · an extra approval for unattributed changes |
| Required checks | none — see below |
| Bypass actors | none |
| Also | `creation` · `deletion` · `non_fast_forward` · `required_signatures` |

## Required checks

**None, and that is the point.** A required context has to be reported by a run, and nothing runs in this repository — every workflow here is `on: workflow_call`, so no job reports one. The ruleset previously demanded a `build` context that has never existed here, which is why it sat `disabled`: switching it on as it stood would have hung every pull request on *"Expected — waiting for status to be reported"*, with no bypass to clear it.

For the same reason there is no `code_scanning` rule — it blocks a pull request when the tool is not configured, and this repository has no `codeql.yml`.

## What publishing must do differently

`publish_workflows.sh` wrote the public subset straight onto `main`. With no bypass that push is refused — *"Changes must be made through a pull request"* — so a republish runs on a branch and lands through a pull request.

## Applying

```sh
# Read it back — this is how the file here was produced
gh api repos/mathewmusango/my-workflows/rulesets

# Replace it in place. Strip id, source and source_type from the body first:
# they are read-only, and the id lives in the URL.
gh api --method PUT repos/mathewmusango/my-workflows/rulesets/23510340 --input rulesets/main.json
```

**Verified.** Read back and probed on 2026-09-25: a fast-forward commit pushed straight at `main` was **refused** (`GH013`, *"Changes must be made through a pull request"*), while `ci/probe-mw` was accepted and then deleted.

**Change flow.** Edit the JSON (export format) → apply it → update this record in the same pull request.
