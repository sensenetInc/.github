# Security policy

How to report a security problem in any Sensenet repo, service or environment, and what happens next. GitHub shows
this policy in every repo of `sensenetInc` that doesn't have its own.

## Reporting a vulnerability

**Don't open a public issue, pull request or discussion for a vulnerability**, and don't post details in chat
channels with people outside Sensenet.

| You are | Report it by | Include |
| --- | --- | --- |
| **Outside Sensenet** (researcher, customer, partner) | Email **security@sensenet.ai** | What you found, where, and how to reproduce it |
| **Sensenet staff or contractor** | Open an issue with the **Security** template in the affected repo, without exploit details, and send the details to **security@sensenet.ai** | The issue link, the affected repos and environments, and the details by email |

A good report has:

- **What's affected:** the repo, service, URL or environment (`rd`, `stage`, `production`).
- **What an attacker could do:** read data, change data, take over an account or a service, deny service.
- **How to reproduce it:** the steps, requests or code, and what you saw.
- **How you found it,** and whether anything was accessed or changed while testing.

**Never send a secret** (password, token, key) in a report, even one you found exposed: say where it is instead.

## What happens next

| When | What |
| --- | --- |
| Within **2 business days** | We acknowledge the report and tell you who is handling it |
| Within **5 business days** | We confirm whether it's a vulnerability, and how severe |
| While we fix it | We keep you informed at least every **7 days** |
| Once fixed | We tell you, and agree with you when and how it can be disclosed |

Target times to a fix in `production`, from confirmation:

| Severity | Examples | Target |
| --- | --- | --- |
| **Critical** | Remote code execution, leaked production credentials, access to every user's data | 2 days |
| **High** | Access to another user's data, bypassing authentication | 7 days |
| **Medium** | Information disclosure with limited impact, missing hardening that an attacker needs other access to exploit | 30 days |
| **Low** | Best-practice gaps with no practical exploit | Next planned release |

## A secret leaked

A password, token, key or `.env` file that reached a commit, an image, a log, an issue or a chat is leaked, even if
it's deleted right away: it stays in git history and in every image built from it.

1. **Rotate it first**, wherever it's used: every copy in every Secret
   ([CONTRIBUTING.md, "Configuration and secrets"](CONTRIBUTING.md#configuration-and-secrets)).
2. **Then remove it** from the repo or the place it leaked.
3. **Report it** with the **Security** template: what leaked (by name, never by value), where, for how long, and
   that it's rotated.

## Supported versions

Fixes go into what runs, through the usual branches: `rd` first, then `stage`, then `production`
([CONTRIBUTING.md, "Branches"](CONTRIBUTING.md#branches)). A critical fix may be fast-tracked through each step,
never skipped.

| Branch | Environment | Gets security fixes |
| --- | --- | --- |
| `production` | Production | Yes |
| `stage` | Staging | Yes, on the way to production |
| `rd` | R&D | Yes, first |
| Any other branch, tag or old image | | No: update to the latest `production` |

## Scope

In scope: every repo in `sensenetInc`, and the services and environments they deploy, including their public
domains.

Out of scope:

- Denial of service, load or volume testing, and spam.
- Social engineering of Sensenet staff, and physical attacks on offices, sites or devices.
- Reports from automated scanners with no demonstrated impact.
- Third-party services we use: report those to their owners.

## Testing in good faith

If you follow this policy, test only in scope, stop as soon as you've confirmed a problem, access no more data than
you need to show it, and give us time to fix it before telling anyone, we won't pursue legal action against you for
that research. Don't access, change or delete data that isn't yours, and don't keep anything you find.

## Security work inside Sensenet

Known gaps and hardening work are tracked as issues with the **Security** template, typed **Security**. They
follow [CONTRIBUTING.md](CONTRIBUTING.md) like any change: a pull request into `rd`, reviewed by someone other than
the author.
