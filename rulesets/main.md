# Ruleset: `main` — record

**Status:** 🟢 applied — live on `refs/heads/main` · **Config:** [`main.json`](main.json)

**Purpose.** `main` binds every actor: pull requests only, one approval, squash only, signed commits, no bypass.

| Field | Value |
| --- | --- |
| Enforcement | `active` |
| Merge methods | `squash` only |
| Approvals | 1 · stale reviews dismissed on push · review threads resolved · an extra approval for unattributed changes |
| Bypass actors | none |
| Required checks | none — every workflow here is `on: workflow_call`, so no job reports a context |
| Also | `creation` · `deletion` · `non_fast_forward` · `required_signatures`; no `code_scanning` rule, there being no `codeql.yml` here |

Publishing lands through a pull request: with no bypass, a direct push to `main` is refused.

## Applying

```sh
# Read it back — this is how the file here was produced
gh api repos/mathewmusango/my-workflows/rulesets

# Replace it in place. Strip id, source and source_type from the body first:
# they are read-only, and the id lives in the URL.
gh api --method PUT repos/mathewmusango/my-workflows/rulesets/23510340 --input rulesets/main.json
```
