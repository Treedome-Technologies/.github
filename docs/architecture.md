# Architecture

## Overview
A repository's `ci` workflow runs a pull request's code without secrets. When it finishes, the repository's small caller workflow (`on: workflow_run`) runs from the default branch, so pull requests can't change it. It calls `bot-reports-shared.yml` here, which reads the run's results through the GitHub API, writes the reports, mints a short-lived token for each bot and posts or updates one comment per bot.

## Components
| Component | Responsibility |
|---|---|
| `profile/README.md` | The organization's "Meet the team" page, and the single source of the bots' personalities |
| `.github/workflows/bot-reports-shared.yml` | Gathers CI data, writes the reports, posts them as the bots |
| Caller `.github/workflows/bot-reports.yml` (in each repo) | Triggers the shared workflow after `ci`, with the org secrets |
| Organization secrets `<PREFIX>_PRIVATE_KEY`, variables `<PREFIX>_CLIENT_ID` | The bots' app credentials |
| `.github/workflows/ci.yml` | Baseline checks and actionlint for this repo |

## Data flow
1. `ci` finishes on a pull request; GitHub starts the caller from the default branch.
2. The shared workflow finds the open pull request by the run's head commit, then reads the run's jobs and the pull request's files with the read-only `GITHUB_TOKEN`.
3. jq writes the reports to files, cleaning every value that came from the pull request.
4. A token is minted for each reporting bot, limited to the one repository.
5. Each bot edits its earlier report comment, found by a hidden marker, or creates one.

## Trust boundaries and threat model
| Boundary | Untrusted input | Threats | Mitigation |
|---|---|---|---|
| Pull request → trusted workflow | Job names, file names, PR author | Script injection, stealing the bot keys | No checkout or execution of PR code (REQ-010); values only through env and files (REQ-011) |
| Report → comment | The same values, posted publicly | Pinging people, phishing links, broken markup | Cleaning and length cap (REQ-012) |
| Bot tokens | — | Misuse beyond a comment | One repository, minimal permissions, revoked at job end (REQ-013) |
| Workflow changes | Pull requests to callers or to this repo | A workflow that leaks secrets | Callers run only from the default branch; every merge needs the owner's approval |

## Design decisions
- Two-stage `ci` → `workflow_run` instead of `pull_request_target`: the stage that holds secrets never sees pull request code.
- Organization secrets instead of per-repo secrets: one place for keys, and new repos need only the caller file.
- One comment per bot, updated in place: no comment pile-up on busy pull requests.
- Pull requests are found by head commit, because fork runs don't list their pull request.
- Plain scripts, no AI: nothing a contributor does can spend AI usage.

## Dependencies
| Dependency | Why | License |
|---|---|---|
| `actions/create-github-app-token` | Mints the bot tokens | MIT |
| `nathanzm/.github` baseline workflow | Secret scan and vendor-file ban in CI | MIT |
| `gh`, `jq` (on GitHub's runner image) | API calls and report text | MIT |
