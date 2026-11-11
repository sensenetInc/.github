# Contributing

How every Sensenet repo is worked on: branches, commits, pull requests and the rules around them. Each repo's
README links here, and adds only what's specific to it: the check to run before pushing, files it must never
commit, and what counts as its contract.

## Branches

| Branch | What | How it changes |
| --- | --- | --- |
| `rd` | R&D environment. All work starts from it and goes back into it | Pull requests from your branches |
| `stage` | Staging | Pull request from `rd` only |
| `production` | Production. **The default branch** | Pull request from `stage` only |
| `feat/...`, `fix/...`, … | Your work, one branch per change, made from `rd` | You push to it |

Repos that aren't deployed (shared config, docs) have one branch, **`main`**: open your pull request into it.
Everything below about `rd`, `stage` and `production` is for the app repos and `wildfire-k8s-clusters`, whose three
branches are the three clusters.

- **Each environment branch is deployed and protected.** Nobody pushes to `rd`, `stage` or `production` directly:
  they change only through pull requests, with a code owner's approval and green checks.
- **`production` is the default branch,** so GitHub shows it first and suggests it as a pull request's base. Your
  pull requests go into **`rd`**: pick it as the base when you open one.
- **Changes move up in order:** `rd`, then `stage`, then `production`. A fix goes into `rd` first and is promoted
  like everything else. Never merge a feature branch into `stage` or `production`.
- **Name your branch** `<type>/<what>`, lowercase with `-` between words: `feat/user-export`, `fix/empty-images`,
  `docs/deploy-steps`. The `pr-title / title` check enforces it ([PR titles and branches](#pr-titles-and-branches)).

## Workflow

```sh
git switch rd && git pull
git switch -c feat/user-export
# ... change, then run the repo's check (its README, "Contributing")
git add -A
git commit -m "feat: add user export"
git push -u origin feat/user-export
```

Then open a pull request into `rd` (not the default `production`):

1. **Fill in the template** GitHub puts in the description: what and why, the task link, how to test it,
   screenshots for anything visible, and its checklist.
2. **For a release**, use its template instead ([Releases](#releases)): `to-stage` for `rd` into `stage`,
   `to-production` for `stage` into `production`.
3. **Merge once every required check is green and it's approved:** into `rd`, by a code owner (`platform`, for now); into `stage` and `production`, by someone from each of `platform`, `release-managers` and
   `security` (one person in several of them covers each). Not your own approval. A new
   push clears earlier approvals, so the reviewer approves what's actually merged.
4. **Delete the branch** after merging.

Keep pull requests small: one feature or fix each, reviewable in one sitting. Rebase on `rd` (or merge `rd` in) if
it moved while you worked.

## Checks on every pull request

GitHub won't merge a pull request until its required checks pass. Which are required is set for the whole
organization, so they're the same in every repo.

| Check | Looks for | When it fails |
| --- | --- | --- |
| `security / secrets` | Passwords, tokens and keys in your commits (gitleaks) | Rotate the secret first, then remove it from your branch ([SECURITY.md](SECURITY.md#a-secret-leaked)) |
| `security / sast` | Security flaws in our own code (Semgrep) | Fix it, or ask `@sensenetInc/security` for an exception |
| `security / dependencies` | Libraries with a known high or critical CVE that has a fix, or a licence we don't allow (Trivy) | Update the library; Renovate's pull requests usually already do |
| `security / iac` | Risky Dockerfiles and Kubernetes manifests (Trivy, Hadolint) | Fix the file, or ask for an exception |
| `pr-title / title` | The PR title and its branch follow [PR titles and branches](#pr-titles-and-branches) | Edit the title (the check re-runs); a wrong branch name needs a new branch and PR |
| `ci-security-review / review` | An AI security review of the change, into `rd` only | Fix the high findings, or `@sensenetInc/security` accepts them with the `security-review-accepted` label |
| `release-source / source` | `stage` takes pull requests from `rd` only, `production` from `stage` only | Open it from the right branch ([Releases](#releases)) |

- **Only new findings block.** What was already there when a check was turned on is tracked separately.
- **Exceptions** live in `.trivyignore`, `.semgrepignore` or `.gitleaksignore`, each with a reason and an expiry,
  and only `@sensenetInc/security` approves changes to them.
- **Turn on the secrets hook** once per clone, so a secret is caught before it's committed: `pipx install pre-commit`,
  then `pre-commit install` in the repo.

## PR titles and branches

Pull requests into `rd` are squash-merged, so each one becomes a single commit and **its title is the commit
message**. Commit messages inside your branch don't matter: write them however you like.

The branch and the PR title both start with a type: a slash in the branch (Git doesn't allow `:` in branch names), a
colon in the title. The `pr-title / title` check enforces both.

| | Format | Example |
| --- | --- | --- |
| Branch | `<type>/<what>`: lowercase, words joined with `-` | `feat/csv-export` |
| PR title | `<type>: <what>`: short, in the imperative ("add", not "added"), no period. No scope in parentheses: the repo already says which service changed | `feat: add CSV export` |

| Type | For |
| --- | --- |
| `feat` | A new feature or behaviour |
| `fix` | A bug fix |
| `perf` | Faster or lighter, behaviour the same |
| `refactor` | Code changed, behaviour not |
| `test` | Tests only |
| `docs` | Documentation only |
| `build` | Build, Dockerfile, packaging |
| `ci` | CI workflows |
| `chore` | Dependencies, tooling, anything else |
| `revert` | Undoes an earlier change (GitHub's own `Revert "…"` title and `revert-…` branch are accepted too) |
| `release` | The release process: setting a version in `CHANGELOG.md` (`release: v0.2.0` on branch `release/v0.2.0`), and naming a promotion (`release: v0.2.0 to stage`) |
| `deploy` | A deploy PR in `wildfire-k8s-clusters`, opened by CI (`deploy: backend to rd 1a2b3c4`) |

More examples: `fix: refuse requests without a token` on `fix/require-token`, `docs: explain the retry policy` on
`docs/retry-policy`.

Add `!` before the colon when the change breaks callers or config: `feat!: drop the v1 perimeter endpoint`, and say
what changes under **Config changes** in the PR's description, marked **Breaking**. Put the why in the PR description: it becomes the commit body.

Promotion pull requests (`rd` → `stage` → `production`) are merge commits and aren't checked, and neither are bots'
(`renovate/…`, `dependabot/…`, `deploy/…`). Still, title a promotion `release: <version> to stage` (or `to production`):
it's the merge commit's message on that branch.

## Rules

1. **One change per commit.** Don't mix a refactor with a feature: each commit should be easy to review and to
   revert on its own.
2. **The repo's check passes before you push** (its README, "Contributing"). Where CI runs it too, a failure blocks
   the merge.
3. **Never commit secrets:** passwords, tokens, API keys, `.env` files, private keys, or URLs with credentials in
   them. `.gitignore` covers the usual ones, and each README names what else its repo must keep out (data,
   generated files, recordings). A secret that was committed is leaked, even after it's deleted: it stays in git
   history and in images built from it. Rotate it, then remove it.
4. **Changed the contract? Say so in the pull request.** The contract is everything another service or the
   platform team relies on: env vars, endpoints, message and file shapes, ports, database schema. Describe it under
   **Config changes** in the PR's description, even small changes (the release PRs collect them for the platform
   team), and update the README where the repo documents it. Each README lists what counts as contract for that
   repo. Something users will notice also gets a line in `CHANGELOG.md` ([Changelog](#changelog)).
5. **No force-push** to `rd`, `stage` or `production`. On your own branch it's fine.
6. **Don't merge your own pull request without a review**, and don't merge one whose CI is red.

## Releases

A release goes up in three pull requests: one sets the version on `rd`, two promote it, each with its own template. GitHub has no chooser for them: open each from
the links below, with `<repo>` replaced, or add `?template=<file>` to a new pull request's URL.

| Step | Pull request | Open it with |
| --- | --- | --- |
| Version | `release/<version>` into `rd`, titled `release: <version>`: sets `## <version> (<date>)` in `CHANGELOG.md` | A normal pull request |
| Release candidate | `rd` into `stage`, titled `release: <version> to stage` | `https://github.com/sensenetInc/<repo>/compare/stage...rd?template=to-stage.md` |
| Release | `stage` into `production`, titled `release: <version> to production` | `https://github.com/sensenetInc/<repo>/compare/production...stage?template=to-production.md` |

**Into `stage`** (`to-stage`): it decides what's in the release.

1. **Check `CHANGELOG.md`.** The version and date are set, and its lines read well for customers.
2. **List every pull request it carries**, with its task.
3. **Hand the Config and database changes to the platform team** for `stage`, collected from the included PRs'
   **Config changes**: they apply them before or with the new image.
4. **Write the QA plan** and name who tests it on `stage`.

**Into `production`** (`to-production`): it ships exactly what was checked on `stage`.

1. **Link the staging pull request** and record its results: who checked it, when, what they found.
2. **Nothing new on `stage`** since it was checked. A fix found on `stage` goes into `rd` and comes up through a new
   staging pull request.
3. **Hand the Config and database changes to the platform team** for `production`: values may differ from `stage`.
4. **Agree a release time**, with someone available to watch it and roll it back.
5. **Write the rollback:** the previous version, and whether it still runs after any schema change.
6. **Approval from each team:** `platform`, `release-managers` (the go/no-go) and `security`. One person who is in
   several of them covers each.
7. **Watch it after the deploy**, as the template's "After the deploy" says.

On merge into `production`, CI tags the version (`vX.Y.Z`, from `CHANGELOG.md`), creates its GitHub Release (the
public notes, then the PR titles since the last release), and sends the public notes to `releases.wildfires.app`. Tags can't be created, moved or deleted by hand.

## Every repo has

GitHub applies this organization's `SECURITY.md`, `CODE_OF_CONDUCT.md`, `SUPPORT.md`, `CONTRIBUTING.md` and
templates to every repo. These it doesn't: each repo keeps its own.

| File | What | Start from |
| --- | --- | --- |
| `README.md` | What it is, how to run it, its variables and "Contributing" | An existing repo's README |
| `CHANGELOG.md` | One entry per release: the public notes ([Changelog](#changelog)) | The template below |
| `LICENSE` | Proprietary: all rights reserved | A copy of this organization's [LICENSE](LICENSE) |
| `.github/CODEOWNERS` | Who approves PRs into `rd` (`platform`, for now) | `templates/repo/.github/CODEOWNERS` in `sensenetInc/github-org` |
| `.gitignore` | At least `.env`, `.idea/`, `.DS_Store`, and the language's build output | |
| `.github/workflows/ci.yml` | The checks above, and for repos that deploy, the image build and release | `templates/repo/` in `sensenetInc/github-org` |
| `.pre-commit-config.yaml`, `renovate.json` | The secrets hook; dependency updates | `templates/repo/` in `sensenetInc/github-org` |

`sensenetInc/github-org` is private: org members can open it.

## Changelog

Every repo has a `CHANGELOG.md` with **public notes only**: what users will notice, in plain language. When a version
reaches `production`, CI tags it, creates its GitHub Release, and publishes the entry on `releases.wildfires.app`.
Newest first, one entry per version:

```markdown
## vX.Y.Z (YYYY-MM-DD)

- Camera streams reconnect on their own after a network drop.
```

- **Versions** follow [semantic versioning](https://semver.org): a breaking change to the contract is a new major
  version, a new feature a minor, a fix a patch. Until `v1.0.0`, a breaking change is a new minor. The
  `release / changelog` check enforces it on the PRs into `stage` and `production`, from the PR titles since the last
  release: `<type>!:` needs a major, `feat:` a minor. A bigger step is allowed; a smaller one or a gap blocks the PR.
- **Unreleased changes** go under `## vX.Y.Z (unreleased)`, at the top, as they're merged into `rd`. The release
  pull request into `stage` sets the date. Nothing for users in a version: `- None`.
- **Nothing internal:** no env vars, endpoints, hostnames, queue names or ticket numbers. It's published as is.
- **Where the internal notes are:** settings and contract changes in each PR's description (**Config changes**),
  collected by the `to-stage` and `to-production` PRs for the platform team. The list of changes is generated: the
  GitHub Release adds every PR title since the last release under **Changes**.
- **What's on `rd`, not released yet:** each app repo's draft release **Next release (rd)** (Releases page), rebuilt
  on every merge into `rd`: every PR title since the last release, grouped, and the smallest next version. Use it to
  write the public lines when you prepare a release.
- **The release workflow reads this format:** the newest `## vX.Y.Z (date)` heading is the version it tags, and that
  entry is what it publishes.
- **Never a secret's value,** only its key.

## Issues

Open issues with this organization's templates: **Feature**, **Improvement**, **Bug**, **Security** or **General
Task**. Blank issues are turned off. Report a security problem with the **Security** template, and keep exploit
details out of public places.
