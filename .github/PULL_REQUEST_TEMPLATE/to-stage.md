<!-- A release candidate: release-candidate/<version> (cut from rd) into stage. Open it with ?template=to-stage.md.
     The title is set for you: release: <version> to stage. -->

## Release

Version (from `CHANGELOG.md`):

What this release brings, in a few lines.

## Included changes

Every pull request merged into `rd` since the last release to `stage`:

- **Feature:** Title
  - PR: #123
  - Task: the task link
  - Design: link, if any
- **Fix:** Title
  - PR: #124
  - Task:

## Config and database changes for stage

Collected from the included PRs' **Config changes**: what the platform team must change for `stage`, before or with
this image (env vars, Secrets, endpoints, schema). Otherwise: `None`.

## Release notes

This version's entry in `CHANGELOG.md` (public), checked for customers: plain language, nothing internal.
Otherwise: `None`.

## QA plan

What to test on `stage`, and who tests it: features, flows, URLs, API calls.

1.
2.

## Checklist

- [ ] The version and date set in `CHANGELOG.md`
- [ ] Every included PR's **Config changes** collected above
- [ ] Release notes checked: plain language, nothing internal
- [ ] Deployed on `rd` and checked there
- [ ] Cluster manifests: this release's `base/` changes in `wildfire-k8s-clusters` promoted to `stage` first (link), or none
- [ ] Config and database changes for `stage` handed to the platform team
- [ ] A tester is named in the QA plan
