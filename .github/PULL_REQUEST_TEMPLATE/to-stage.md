<!-- A release candidate: rd into stage. Open it with ?template=to-stage.md.
     How we release: https://github.com/sensenetInc/.github/blob/production/CONTRIBUTING.md#releases -->

## Release

Version (from `CHANGELOG.md`):

What this release brings, in a few lines.

## Included changes

Every pull request merged into `rd` since the last release to `stage`:

- **Feature:** Title
  - PR: #123
  - Task: Jira epic, or the task key for a single task
  - Ideation: Confluence link, if any
- **Fix:** Title
  - PR: #124
  - Task:

## Config and database changes for stage

Copied from `CHANGELOG.md`: what the platform team must change in the deployments repo for `stage`, before or with
this image (env vars, Secrets, endpoints, schema). Otherwise: `None`.

## QA plan

What to test on `stage`, and who tests it: features, flows, URLs, API calls.

1.
2.

## Checklist

- [ ] Every included change has a `CHANGELOG.md` entry under this version
- [ ] Deployed on `rd` and checked there
- [ ] Config and database changes for `stage` handed to the platform team
- [ ] A tester is named in the QA plan
