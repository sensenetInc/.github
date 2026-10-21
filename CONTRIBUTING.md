# Contributing

How every Sensenet repo is worked on: branches, commits, pull requests and the rules around them. Each repo's
README links here, and adds only what's specific to it: the check to run before pushing, files it must never
commit, and which changes go in its `CHANGELOG.md`.

## Branches

| Branch | What | How it changes |
| --- | --- | --- |
| `rd` | R&D environment. The default branch: all work starts from it | Pull requests from your branches |
| `stage` | Staging | Pull request from `rd` only |
| `production` | Production | Pull request from `stage` only |
| `feat/...`, `fix/...`, … | Your work, one branch per change | You push to it |

- **Each environment branch is deployed.** Every push to `rd`, `stage` or `production` builds and pushes that
  environment's image, tagged with the branch name (`:rd`, `:stage`, `:production`) and the commit (`:sha-<commit>`).
  So nobody pushes to them directly: they change only through pull requests.
- **Changes move up in order:** `rd`, then `stage`, then `production`. A fix goes into `rd` first and is promoted
  like everything else. Never merge a feature branch into `stage` or `production`.
- **Name your branch** `<type>/<what>`, with the same types as commit messages: `feat/camera-list`,
  `fix/empty-images`, `docs/deploy-steps`.

## Workflow

```sh
git switch rd && git pull
git switch -c feat/camera-list
# ... change, then run the repo's check (its README, "Contributing")
git add -A
git commit -m "feat: show the camera list"
git push -u origin feat/camera-list
```

Then open a pull request into `rd`:

1. **Fill in the template** GitHub puts in the description: what and why, the task link, how to test it,
   screenshots for anything visible, and its checklist.
2. **For a release**, use its template instead ([Releases](#releases)): `to-stage` for `rd` into `stage`,
   `to-production` for `stage` into `production`.
3. **Merge once CI is green and one person has approved it.** Not your own approval.
4. **Delete the branch** after merging.

Keep pull requests small: one feature or fix each, reviewable in one sitting. Rebase on `rd` (or merge `rd` in) if
it moved while you worked.

## Commit messages

`<type>: <what>`, short, lowercase, in the imperative ("add", not "added"), with no period at the end.

| Type | For |
| --- | --- |
| `feat` | A new feature or behaviour |
| `fix` | A bug fix |
| `refactor` | Code changed, behaviour not |
| `test` | Tests only |
| `docs` | Documentation only |
| `ci` | CI workflows |
| `chore` | Dependencies, tooling, anything else |

Examples: `feat: add BC Wildfire Service perimeters feed`, `fix: refuse RTSP reads without credentials`,
`docs: explain the shard count`.

Add a body, after a blank line, when the why isn't obvious from the change.

## Rules

1. **One change per commit.** Don't mix a refactor with a feature: each commit should be easy to review and to
   revert on its own.
2. **The repo's check passes before you push** (its README, "Contributing"). Where CI runs it too, a failure blocks
   the merge.
3. **Never commit secrets:** passwords, tokens, API keys, `.env` files, private keys, or URLs with credentials in
   them. `.gitignore` covers the usual ones, and each README names what else its repo must keep out (data,
   generated files, recordings). A secret that was committed is leaked, even after it's deleted: it stays in git
   history and in images built from it. Rotate it, then remove it.
4. **Changed the contract? Update `CHANGELOG.md` in the same pull request.** The contract is everything another
   service or the platform team relies on: env vars, endpoints, message and file shapes, ports, database schema.
   List it under **Config** (and **Database**, where the repo has it), even small changes: the platform team deploys
   from the changelog. Each README lists what counts as contract for that repo.
5. **No force-push** to `rd`, `stage` or `production`. On your own branch it's fine.
6. **Don't merge your own pull request without a review**, and don't merge one whose CI is red.

## Releases

A release goes up in two pull requests, each with its own template. GitHub has no chooser for them: open each from
the links below, with `<repo>` replaced, or add `?template=<file>` to a new pull request's URL.

| Step | Pull request | Open it with |
| --- | --- | --- |
| Release candidate | `rd` into `stage` | `https://github.com/sensenetInc/<repo>/compare/stage...rd?template=to-stage.md` |
| Release | `stage` into `production` | `https://github.com/sensenetInc/<repo>/compare/production...stage?template=to-production.md` |

**Into `stage`** (`to-stage`): it decides what's in the release.

1. **Check `CHANGELOG.md`.** Every change since the last release has an entry, under the version being released.
2. **List every pull request it carries**, with its task.
3. **Hand the Config and database changes to the platform team** for `stage`: they apply them in the deployments
   repo before or with the new image.
4. **Write the QA plan** and name who tests it on `stage`.

**Into `production`** (`to-production`): it ships exactly what was checked on `stage`.

1. **Link the staging pull request** and record its results: who checked it, when, what they found.
2. **Nothing new on `stage`** since it was checked. A fix found on `stage` goes into `rd` and comes up through a new
   staging pull request.
3. **Hand the Config and database changes to the platform team** for `production`: values may differ from `stage`.
4. **Agree a release time**, with someone available to watch it and roll it back.
5. **Write the rollback:** the previous `sha-<commit>` tag, and whether it still runs after any schema change.
6. **Watch it after the deploy**, as the template's "After the deploy" says.

## Issues

Open issues with this organization's templates: **Feature**, **Improvement**, **Bug**, **Security** or **General
Task**. Blank issues are turned off. Report a security problem with the **Security** template, and keep exploit
details out of public places.
