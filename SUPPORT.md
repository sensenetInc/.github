# Getting help

Where to go with a question or a problem in any Sensenet repo. GitHub shows this page in every repo of
`sensenetInc` that doesn't have its own.

| You need | Go to |
| --- | --- |
| How a repo works, how to run it, its variables | The repo's README |
| How we work: branches, commits, pull requests, releases, config and secrets | [CONTRIBUTING.md](CONTRIBUTING.md) |
| A bug, a feature, an improvement or a task | A new issue in the repo, with its template ([CONTRIBUTING.md, "Issues"](CONTRIBUTING.md#issues)) |
| A security problem, or a leaked secret | [SECURITY.md](SECURITY.md): never a public issue |
| A deploy, an environment, a Secret, a namespace or the registry | The platform team, with a **General Task** issue in the deployments repo |
| Behaviour that breaks the [code of conduct](CODE_OF_CONDUCT.md) | conduct@sensenet.ai, or your manager |

## Before you ask

- **Search the issues** of the repo, open and closed: it may be known, or already fixed on `rd`.
- **Check the repo's `CHANGELOG.md`:** a change to its env vars, endpoints or messages is listed under **Config**.
- **Say which environment** (`rd`, `stage`, `production`), which image tag (`sha-<commit>`) and what you saw: the
  error, the log lines, a screenshot.
- **Never paste a secret** in an issue or a chat, even to show it's wrong. Name it by its key instead
  (`POSTGRES_PASSWORD in smoke-secrets`).
