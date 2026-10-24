<!-- A release: stage into production. Open it with ?template=to-production.md.
     It ships what was checked on stage, and nothing else.
     How we release: https://github.com/sensenetInc/.github/blob/production/CONTRIBUTING.md#releases -->

## Release

Version (from `CHANGELOG.md`):
Staging pull request: #

What this release brings, in a few lines. The full list of changes is in the staging pull request.

## Staging results

Who checked it on `stage`, when, and what they found (issues fixed, issues accepted).

## Config and database changes for production

What the platform team must change for `production`, before or with this image. Values
may differ from `stage`: other hosts, other Secrets. Otherwise: `None`.

## Release time

When it goes out, and who is available to watch it and roll it back.

## Rollback

- Previous version: `sha-`
- Does it still run after this release's schema changes? Yes / no: if no, how to go back instead.

## After the deploy

What to watch, and for how long: logs, dashboards, error rates, key flows to try.

1.
2.

## Checklist

- [ ] Approved on `stage` (staging results above)
- [ ] No new commits on `stage` since it was checked
- [ ] Config and database changes for `production` handed to the platform team
- [ ] Release time agreed, and someone available to roll back
