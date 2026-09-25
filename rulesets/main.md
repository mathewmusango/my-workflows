# Ruleset: `branch: main` — record

**Status:** 🟢 applied — live on `refs/heads/main` · **Config:** [`main.json`](main.json)

**Purpose.** `main` binds every actor: pull requests only, one approval, squash only, signed commits, no bypass.

| Field | Value |
| --- | --- |
| Enforcement | `active` |
| Merge methods | `squash` only |
| Approvals | 1 · stale reviews dismissed on push · review threads resolved · an extra approval for unattributed changes |
| Required checks | **none** — see below |
| Bypass actors | none |
| Also | `creation` · `deletion` · `non_fast_forward` · `required_signatures` |

## No required checks here, unlike every consumer

A required context has to be reported by a run, and **nothing runs in this repository** — every workflow here is `on: workflow_call`, so there is no job that reports one. A context no run reports hangs every pull request on *"Expected — waiting for status to be reported"*, and with `bypass_actors: []` nothing can clear it.

**The ruleset used to carry exactly that mistake.** It was `disabled`, and its `required_status_checks` demanded a `build` context this repository has never reported. The rewrite on 2026-09-25 dropped it and turned enforcement on.

**No `code_scanning` rule either, and deliberately.** The consumers carry one because they run CodeQL; this repository has no `codeql.yml`. That rule blocks a pull request when *"the tool is not configured for the repository"*, so adding it here would freeze every pull request instead of scanning anything. It belongs in the same change as a CodeQL workflow.

## What this changes for publishing

`publish_workflows.sh` writes the public subset **on `main`** here, and the push was the last step of a release. Removing the admin bypass makes that push impossible:

```
! [remote rejected] ... -> main (push declined due to repository rule violations)
remote: - Changes must be made through a pull request.
```

So a republish now runs on a **branch** and lands through a pull request, the script's `--commit` step included. That is the deliberate cost of `bypass_actors: []`, and it is what the rest of the account already does.

## Applying

```sh
# Read it back — this is how the file here was produced
gh api repos/mathewmusango/my-workflows/rulesets

# Replace it in place. Strip id, source and source_type from the body first:
# they are read-only, and the id lives in the URL.
gh api --method PUT repos/mathewmusango/my-workflows/rulesets/23510340 --input rulesets/main.json
```

**Verified.** Read back and probed on 2026-09-25: a fast-forward commit pushed straight to `main` was **refused** (`GH013`, *"Changes must be made through a pull request"*), while `ci/probe-mw` was accepted and then deleted. A badly named branch is refused by [`branches: all`](all.md).

**Change flow.** Edit the JSON (export format) → apply it → update this record in the same pull request.
