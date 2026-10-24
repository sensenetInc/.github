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
- **Name your branch** `<type>/<what>`, with the same types as commit messages: `feat/user-export`,
  `fix/empty-images`, `docs/deploy-steps`.

## Workflow

```sh
git switch rd && git pull
git switch -c feat/user-export
# ... change, then run the repo's check (its README, "Contributing")
git add -A
git commit -m "feat: add user export"
git push -u origin feat/user-export
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

Examples: `feat: add CSV export`, `fix: refuse requests without a token`,
`docs: explain the retry policy`.

Add a body, after a blank line, when the why isn't obvious from the change.

## Rules

1. **One change per commit.** Don't mix a refactor with a feature: each commit should be easy to review and to
   revert on its own.
2. **The repo's check passes before you push** (its README, "Contributing"). Where CI runs it too, a failure blocks
   the merge.
3. **Never commit secrets:** passwords, tokens, API keys, `.env` files, private keys, or URLs with credentials in
   them ([Configuration and secrets](#configuration-and-secrets)). `.gitignore` covers the usual ones, and each README names what else its repo must keep out (data,
   generated files, recordings). A secret that was committed is leaked, even after it's deleted: it stays in git
   history and in images built from it. Rotate it, then remove it.
4. **Changed the contract? Update `CHANGELOG.md` in the same pull request.** The contract is everything another
   service or the platform team relies on: env vars, endpoints, message and file shapes, ports, database schema.
   List it under **Config** (and **Database**, where the repo has it), even small changes: the platform team deploys
   from the changelog. Each README lists what counts as contract for that repo.
5. **No force-push** to `rd`, `stage` or `production`. On your own branch it's fine.
6. **Don't merge your own pull request without a review**, and don't merge one whose CI is red.

## Configuration and secrets

Every value a service reads from its environment is one of two kinds, and they never mix:

| | Config | Secret |
| --- | --- | --- |
| **What** | Anything that's safe to read: hosts, ports, URLs, queue names, intervals, limits, feature flags, model names, usernames that aren't a login | Anything that grants access: passwords, tokens, API keys, private keys, and the usernames that go with them |
| **Test** | Would it be fine on a public screen? | Would you need to rotate it if it leaked? |
| **Lives in** | The app's repo, `config/<environment>/<workload>.env`, committed ([Deploy config files](#deploy-config-files)) | A Kubernetes Secret, `<workload>-secrets`, in the cluster only |
| **Becomes** | The ConfigMap `<workload>-config`, applied by CD | The Secret, created with `kubectl` |
| **Changed by** | A pull request, reviewed like code: CD deploys it when it's merged | `kubectl create secret` or `kubectl patch secret`, by someone with access |
| **History** | `git log`: who, when, why; rolled back with `git revert` | None: write the change down where your team tracks operations |
| **Pods pick it up** | By themselves: CD restarts them with the new settings | Only after a restart (`kubectl rollout restart`) |

**Both reach the container the same way**, with `envFrom`: every key becomes an env var of the same name, so the
code doesn't know or care which kind a value is.

```yaml
envFrom:
  - configMapRef:
      name: <workload>-config     # settings, from config/<environment>/<workload>.env, in git
  - secretRef:
      name: <workload>-secrets    # passwords, tokens and keys, in the cluster only
```

Rules:

1. **Every workload has its own config file and its own Secret.** Never share a Secret between two workloads, even
   when they need the same password: store a copy in each. A shared Secret ties workloads together, and one can't
   change it without restarting the other. A copy must be updated everywhere when the password is rotated: each
   app's README lists where its copies are.
2. **When in doubt, it's a secret.** Moving a value from a Secret to config later is easy; un-leaking it isn't.
3. **No secret in a config file**, in a manifest, in a `CHANGELOG.md`, a pull request, an issue or a chat. Refer to
   it by its key (`DB_PASSWORD in <workload>-secrets`), never by its value.
4. **No setting in a Secret.** A setting hidden in a Secret can be changed without review, and nobody can see what
   it is or was.
5. **A new env var** goes in the right place in the same pull request as the code that reads it: the config files
   (`config/<environment>/<workload>.env`, every environment), or the Secret's creation commands in the app's README. List it in `CHANGELOG.md`, under
   **Config**.
6. **In each repo**, the settings CD deploys are in `config/<environment>/<workload>.env`
   ([Deploy config files](#deploy-config-files)).
7. **Locally**, a project's `.env.example` lists every key with config values filled in and secrets as
   `CHANGE_ME`. Copy it to `.env` (git-ignored) and fill in the secrets: they're the same keys as in the cluster.

## Deploy config files

Every repo that deploys to Kubernetes has its settings in `config/<environment>/<workload>.env`:

```text
config/
  rd/
    <workload>.env      # what R&D runs
  stage/                # added with the first stage deploy
  production/           # added with the first production deploy
```

- **CD deploys it.** On a push to `rd`, `stage` or `production`, CD reads `config/<branch>/<workload>.env` and applies
  it as the ConfigMap `<workload>-config`, next to the new image. A repo with several workloads (e.g. one per scheduled job)
  has one file per workload.
- 🚫 **Never delete, rename or move these files or folders.** CD finds them by their exact path: a missing file means
  the deploy fails, or runs with no settings. Changing the layout is a change to CD itself: agree it with the
  platform team first, and update CD in the same release.
- **Every branch has every environment's file.** A release merges `rd` into `stage`, so the files travel with it,
  and each environment still reads its own. To change a `stage` or `production` setting, edit its file in a pull
  request into `rd`, like any change: it's deployed with the next release to that environment.
- **Settings only.** The rules of [Configuration and secrets](#configuration-and-secrets) apply: no password, token
  or key, ever. Secrets stay in `<workload>-secrets` in the cluster.
- **Same format as `.env`:** `KEY=value` lines, `#` comments. No quotes, no `export`, no variable expansion: values
  are taken literally.

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

## Every repo has

GitHub applies this organization's `SECURITY.md`, `CODE_OF_CONDUCT.md`, `SUPPORT.md`, `CONTRIBUTING.md` and
templates to every repo. These it doesn't: each repo keeps its own.

| File | What | Start from |
| --- | --- | --- |
| `README.md` | What it is, how to run it, its variables, "Kubernetes" and "Contributing" | An existing repo's README |
| `CHANGELOG.md` | One entry per release, read by the platform team ([Changelog](#changelog)) | The template below |
| `LICENSE` | Proprietary: all rights reserved | A copy of this organization's [LICENSE](LICENSE) |
| `.github/CODEOWNERS` | Who reviews which paths | This repo's [.github/CODEOWNERS](.github/CODEOWNERS), with the repo's own owners |
| `.github/workflows/image.yml` | Builds and pushes the image ([CI](#ci)) | |
| `config/rd/<workload>.env` | The settings CD deploys ([Deploy config files](#deploy-config-files)) | |
| `.env.example` | Every env var, secrets as `CHANGE_ME` ([Configuration and secrets](#configuration-and-secrets)) | |
| `.gitignore` | At least `.env`, `.idea/`, `.DS_Store`, and the language's build output | |

## Changelog

Every repo has a `CHANGELOG.md`: the platform team deploys from it, so a release isn't handed over without its
entry. Newest first, one entry per version:

```markdown
## vX.Y.Z (YYYY-MM-DD)

Changes:
- What changed, for someone who uses the service: a feature, a fix, a behaviour.

Config:
- New env var `FOO` (required / default `bar`): what it does.
- Removed env var `BAR`: what replaces it.
- None

Database:
- New column `checks.fps`, added at startup; older versions ignore it.
- None
```

- **Versions** follow [semantic versioning](https://semver.org): a breaking change to the contract is a new major
  version, a new feature a minor, a fix a patch. Until `v1.0.0`, a breaking change is a new minor.
- **Unreleased changes** go under `## vX.Y.Z (unreleased)`, at the top, as they're merged into `rd`. The release
  pull request into `stage` sets the date.
- **Config** lists every change to the contract (env vars, endpoints, message and file shapes, ports, queues,
  volumes), even small ones, or `None`. **Database** is for repos with a schema.
- **Never a secret's value,** only its key and which Secret it's in.

## CI

Every repo that ships an image builds it on every push to `rd`, `stage` or `production`, with two tags:

| Tag | Moves? | Use |
| --- | --- | --- |
| `:<branch>`, e.g. `:rd` | Yes, to the latest build of that branch | What the environment runs |
| `:sha-<commit>`, e.g. `:sha-1a2b3c4` | No | Rollbacks, and seeing exactly what's running |

Nobody needs registry credentials on their own machine: only CI pushes images.

## Issues

Open issues with this organization's templates: **Feature**, **Improvement**, **Bug**, **Security** or **General
Task**. Blank issues are turned off. Report a security problem with the **Security** template, and keep exploit
details out of public places.
