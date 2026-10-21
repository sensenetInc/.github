# sensenetInc/.github

The organization's shared files: how we work, and the issue and pull request templates every repo uses. GitHub
applies them to every repo in `sensenetInc` that doesn't have its own, so they're written once here instead of
copied into each repo.

## Files

| Path | What | Used by |
| --- | --- | --- |
| `CONTRIBUTING.md` | Branches, workflow, commit messages, rules, releases | Every repo's README, "Contributing", links to it. GitHub also links it when someone opens an issue or pull request |
| `.github/pull_request_template.md` | The default pull request description: a change into `rd` | Every new pull request |
| `.github/PULL_REQUEST_TEMPLATE/to-stage.md` | A release candidate: `rd` into `stage`. Included changes, config for `stage`, QA plan | Pull requests opened with `?template=to-stage.md` in the URL |
| `.github/PULL_REQUEST_TEMPLATE/to-production.md` | A release: `stage` into `production`. Staging results, config for `production`, release time, rollback, what to watch | Pull requests opened with `?template=to-production.md` in the URL |
| `.github/ISSUE_TEMPLATE/*.md` | Feature, Improvement, Bug, Security and General Task | The **New issue** chooser |
| `.github/ISSUE_TEMPLATE/config.yml` | Turns off blank issues | The **New issue** chooser |

## How GitHub uses them

- **A repo's own file wins.** A repo with its own `CONTRIBUTING.md`, `pull_request_template.md` or
  `ISSUE_TEMPLATE/` folder uses that instead of these, for that kind of file only. Don't add one unless the repo
  really works differently.
- **This repo must be public** for GitHub to apply its files to the other repos, even private ones. If it's
  private, nothing here is applied: repos get no templates, and only the README links reach `CONTRIBUTING.md`.
- **Only the default branch counts:** `production`. Changes apply as soon as they're merged there.
- **Issue types:** each issue template sets `type:` (Feature, Improvement, Bug, Security, Task). A type takes effect
  only if it exists in the organization's settings, under **Issue types**. GitHub creates Task, Bug and Feature;
  Improvement and Security must be added there.
- **Pull request templates:** the default one fills every new pull request. GitHub has no chooser for the others:
  open one by adding `?template=<file name>` to the new pull request's URL. `CONTRIBUTING.md`, "Releases", has
  ready-made links for both release templates, e.g.
  `https://github.com/sensenetInc/<repo>/compare/stage...rd?template=to-stage.md`.

## Changing them

Through a pull request into `production`, like any other change ([CONTRIBUTING.md](CONTRIBUTING.md)). Everything
here applies to every repo at once, so:

- **Keep templates short.** Every field is something people fill in on every issue or pull request.
- **Repo-specific rules stay in that repo's README**, in its "Contributing" section: the check to run, what it must
  never commit, what goes in its changelog. Only what's true for every repo belongs here.
- **Renaming a template** breaks `?template=` links and bookmarks that use the old name.
- **Check the result** after merging: open a new issue and a new pull request in any repo, and look at what's
  filled in.
