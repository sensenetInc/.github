<!-- Into rd. For a release, add a template to this page's URL: ?template=to-stage.md (release-candidate/<version>
     into stage) or ?template=to-production.md (stage into production). -->

## Summary

What this pull request does, and why it's needed.

Task:

## What changed

-
-

## How to test

Exact steps for the reviewer: URLs, API calls or commands, and what to expect.

1.
2.

## Screenshots / videos

Before and after, for any change you can see. Otherwise: `N/A`.

## Config changes

Every change to the contract: env vars (and in which `config/<cluster>/*.env`), Secrets, endpoints, message and file
shapes, ports, schema. Mark a breaking one **Breaking** (and name the branch `<type>!/<what>`: CI titles it `<type>!: …`). The release PRs collect
these for the platform team. Otherwise: `None`.

## Technical notes

Design decisions, edge cases, anything the reviewer should look at closely. Otherwise: `N/A`.

## Checklist

- [ ] The repo's check passes (its README, "Contributing")
- [ ] The branch's type (the title is written from it) fits the change, since it sets the version: `feat` = new for users (minor), `fix` = a bug, `sec` = a security fix (patch), `!` = others must change (major). **Code owner:** check it
- [ ] **Config changes** above filled in (or `None`), and the README updated if it documents the contract
- [ ] `CHANGELOG.md` not touched: the release drafts its notes from the PR titles, so the branch reads as the line customers will see (`feat/camera-groups-by-site` → "Camera groups by site.")
- [ ] No secrets or credentials in the diff
- [ ] Nothing changed under `.github/` (workflows, CODEOWNERS, templates), or it's a `ci:` PR approved by `platform`. **Code owner:** check **Files changed** before you merge
- [ ] Every required check is green; the security review's comments are answered (fixed, or accepted by security)
