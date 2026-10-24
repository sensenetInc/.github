# Changelog

What changed in the files every `sensenetInc` repo inherits. They apply to every repo as soon as they're merged
into `production`, so each entry says what changes for the other repos, under **Affects every repo**.

## v1.1.0 (unreleased)

Changes:
- `SECURITY.md`: how to report a vulnerability, response and fix targets, what to do when a secret leaks, supported
  branches, scope and good-faith testing.
- `CODE_OF_CONDUCT.md`: the Contributor Covenant 2.1, adapted, with reporting to conduct@sensenet.ai.
- `SUPPORT.md`: where to go with a question or a problem.
- `LICENSE`: proprietary, all rights reserved.
- `.github/CODEOWNERS`: review owners for this repo.
- `profile/README.md`: the organization's public profile page.
- `CONTRIBUTING.md`: new sections "Every repo has", "Changelog" and "CI".

Affects every repo:
- GitHub shows `SECURITY.md`, `CODE_OF_CONDUCT.md` and `SUPPORT.md` in every repo without its own, and links the
  security policy from the **New issue** page.
- Each repo should add its own `LICENSE` and `.github/CODEOWNERS` (`CONTRIBUTING.md`, "Every repo has"): GitHub
  doesn't inherit those.

## v1.0.0 (2026-10-21)

Changes:
- `CONTRIBUTING.md`: branches, workflow, commit messages, rules, configuration and secrets, deploy config files,
  releases and issues. R&D's deploy config file is `config/rd/<workload>.env` (clarified 2026-10-23).
- Pull request templates consolidated: the default one (into `rd`), `to-stage.md` (release candidate) and
  `to-production.md` (release).
- Issue templates updated: Feature, Improvement, Bug, Security and General Task, each with its issue type. Blank
  issues turned off.
- `README.md`: what each file is, and how GitHub applies them.

Affects every repo:
- Every repo's README, "Contributing", links to `CONTRIBUTING.md` instead of repeating it.
- New pull requests start from the new default template; releases open theirs with `?template=to-stage.md` or
  `?template=to-production.md`.
- The **Improvement** and **Security** issue types must exist in the organization's settings, under **Issue types**.

## v0.3.0 (2026-10-14)

Changes:
- Feature issue template reworked.

Affects every repo:
- New Feature issues use the new template.

## v0.2.0 (2025-11-25)

Changes:
- First pull request templates.

Affects every repo:
- New pull requests start from a template.

## v0.1.0 (2025-07-21)

Changes:
- First issue templates.

Affects every repo:
- The **New issue** chooser offers the organization's templates.
