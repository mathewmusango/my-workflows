# Ruleset: `tag: v*` — record

**Status:** 🟢 applied — live on `refs/tags/v*` · **Config:** [`tags.json`](tags.json)

**Purpose.** Release tags are immutable: a published tag cannot be moved.

| Field | Value |
| --- | --- |
| Target | `refs/tags/v*` |
| Required checks | none — nothing runs here to resolve one |
| Bypass actors | none |
| Rules | `non_fast_forward` · `update` |

**No `creation` rule, and that is load-bearing.** With `bypass_actors` empty, `creation` refuses the push outright — *"Cannot create ref due to creations being restricted"*, verified here on 2026-09-25 — so a tag ruleset carrying it could cut no release at all.

**No `deletion` rule**, which leaves a mistaken tag removable rather than needing a temporary ruleset edit.

## Applying

```sh
# Read it back — this is how the file here was produced
gh api repos/mathewmusango/my-workflows/rulesets/24012293

# Replace it in place. Strip id, source and source_type from the body first:
# they are read-only, and the id lives in the URL.
gh api --method PUT repos/mathewmusango/my-workflows/rulesets/24012293 --input rulesets/tags.json
```
