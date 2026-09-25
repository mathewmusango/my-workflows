# Ruleset: `tag: v*` — record

**Status:** 🟢 applied — live on `refs/tags/v*` · **Config:** [`tags.json`](tags.json)

**Purpose.** Release tags are immutable and cannot be removed.

| Field | Value |
| --- | --- |
| Target | `refs/tags/v*` |
| Required checks | none — nothing runs here to resolve one |
| Bypass actors | none |
| Rules | `deletion` · `non_fast_forward` · `update` |

The same shape as `my-portfolio`'s `tag: v*`, with two of its rules left out because neither can work here:

- **No `creation`.** With `bypass_actors` empty it refuses the push outright — *"Cannot create ref due to creations being restricted"*, verified here on 2026-09-25 — so a tag ruleset carrying it could cut no release at all.
- **No `required_status_checks`.** `my-portfolio` requires `build` there because its tags deploy; nothing runs in this repository, so there is no context to resolve against a tag.

## Applying

```sh
# Read it back — this is how the file here was produced
gh api repos/mathewmusango/my-workflows/rulesets/24012293

# Replace it in place. Strip id, source and source_type from the body first:
# they are read-only, and the id lives in the URL.
gh api --method PUT repos/mathewmusango/my-workflows/rulesets/24012293 --input rulesets/tags.json
```
