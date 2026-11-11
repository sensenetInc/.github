<!-- A hotfix: a hotfix/<what> branch made from production, into production. Open it with ?template=hotfix.md.
     Only for a production bug that can't wait for the next release. It fixes that bug and nothing else.
     How: https://github.com/sensenetInc/.github/blob/main/CONTRIBUTING.md#hotfix -->

## Hotfix

Version (from `CHANGELOG.md`, a patch of the running version, e.g. v1.4.0 -> v1.4.1):
Incident or issue:

What's broken in production, who it affects, and why it can't wait for the next release from `rd`.

## The fix

What changed, in a few lines. Keep it to the bug: no refactoring, no features.

## How it was checked

Where it was tried (locally, on a copy of the data, on `stage`) and what showed it works.

## Config and database changes for production

What the platform team must change for `production`, before or with this image. Otherwise: `None`.

## Rollback

- Previous version:

## Forward-port to rd

The same fix, cherry-picked onto a `fix/<what>` branch from `rd`, with this version's `CHANGELOG.md` entry placed
below `rd`'s unreleased one. Without it, the next release brings the bug back.

Pull request into `rd`: #

## Checklist

- [ ] Branch made from `production` (the `release-source / source` check confirms it carries nothing from `rd`)
- [ ] Title `fix: <what>`, branch `hotfix/<what>`
- [ ] `CHANGELOG.md`: a new entry, exactly the next patch version, with its date and public line (or `- None`)
- [ ] Approved by `platform`, a release manager (the go/no-go) and `security`
- [ ] Forward-port pull request into `rd` opened

On merge, CI tags the version from `CHANGELOG.md` and creates its GitHub Release. Don't create the tag by hand.
