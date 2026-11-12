# Changelog

What changed in the files every `sensenetInc` repo inherits. They apply to every repo as soon as they're merged
into `main`, so each entry says what changes for the other repos, under **Affects every repo**.

## v1.1.0 (unreleased)

Changes:
- `CONTRIBUTING.md`: the checks on every pull request, the PR title as the commit message, PR titles as `<type>: <what>` (no scope) and branches as `<type>/<what>` (squash into `rd`, merge commits for promotions), approval by `platform` or `release-managers` into `rd` and by `platform`, `release-managers` and `security` into `stage` and `production`, one approvals into `production`,
  release tags made by CI, `docs/` in every repo, the secrets pre-commit hook.
- Changelog format: `### Release notes` (public), `### Changes`, `### Config`, `### Database` headings.
- Pull request templates: a release note line, docs and checks in the checklist; release notes checked in
  `to-stage`; release manager approval in `to-production`.
- Default branch `production` for every repo: the only branch of repos that aren't deployed, and the default of the app
  repos (`rd`, `stage`, `production`). This repo keeps `main`; links to its files use `blob/main/`.
- `README.md`: what never goes in this public repo; shared CI lives in `sensenetInc/github-org`.

Affects every repo:
- `CHANGELOG.md` uses the new headings from its next entry, with a **Release notes** section.
- Every repo gets `docs/`, `ci.yml`, `.pre-commit-config.yaml` and `renovate.json` from `sensenetInc/github-org`'s
  `templates/repo/`.

## v1.0.0 (2026-10-23)

Changes:
- Init

Affects every repo:
- None
