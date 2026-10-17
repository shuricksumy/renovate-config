# renovate-config

Shared [Renovate](https://docs.renovatebot.com/) presets for my repositories.

## Use

In a repository's `renovate.json`:

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["github>shuricksumy/renovate-config"]
}
```

Rules that belong to one repository (custom managers, version holds) go below `extends`
in that repository's own file; they are applied after the preset.

## What `default.json` does

- `config:recommended`, plus digest pins for GitHub Actions and for images in Dockerfiles
  (compose files keep plain tags: they are mostly examples people copy).
- Own images (`ghcr.io/shuricksumy/**`) are ignored: the repositories build them.
- No Dependency Dashboard issue; failed updates show in the Mend portal
  (https://developer.mend.io/github/shuricksumy). A repository can turn it back on with
  `"dependencyDashboard": true`.
- Merges by itself, once the required checks pass: GitHub Actions patch bumps and pins, image
  digest refreshes and pins. Everything else waits for a review.

**Auto-merge is only safe where the default branch requires passing CI.** Without required checks
GitHub merges at once. For such a repository add the opt-out:

```json
{ "extends": ["github>shuricksumy/renovate-config", "github>shuricksumy/renovate-config:no-automerge"] }
```

## Gotchas

- An image found twice (a custom regex manager *and* `FROM`/compose) must be turned off in one of
  them (`matchManagers` + `"enabled": false`).
- Never digest-pin a version that is also reused as a tag, file name or URL (an `ARG` used
  elsewhere): add `"pinDigests": false` for that package.
- The Mend Renovate app must have access to each repository that uses this preset.
