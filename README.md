# .github

Organization-level files and the bots' workflows for Treedome Technologies.

- [`profile/README.md`](profile/README.md): the "Meet the team" page shown on the [organization's overview](https://github.com/Treedome-Technologies).
- **Bot actions** ([`bot.yml`](.github/workflows/bot.yml)): a bot does one thing on one org repo under its own name, run by the organization owner.
- **Bot reports** ([`bot-reports-shared.yml`](.github/workflows/bot-reports-shared.yml)): scripted reports on contributors' pull requests.
- Design docs: [requirements](docs/requirements.md), [architecture](docs/architecture.md) and [test plan](docs/test-plan.md).

CI runs the shared standards workflow (secret scan, vendor-file ban, dependency review, actionlint, and a gate for each language found) from [`nathanzm/.github`](https://github.com/nathanzm/.github) at `@main`. The only pinned action here is GitHub's token action, which Dependabot keeps current; no other Treedome repo pins anything.

## Bot actions

Only the organization owner can run it, with their own gh login:

```sh
gh workflow run bot.yml -R Treedome-Technologies/.github \
  -f bot=spongebot-steelpants -f action=review -f repo=scratch -f number=3 -f body='Looks good to me.'
```

| `action` | Needs | Does |
|---|---|---|
| `open-pr` | `title`, `head` (`base` defaults to `main`; `body` optional) | Opens a PR |
| `review` | `number`, `body` | Posts a COMMENT review (never APPROVE) |
| `comment` | `number`, `body` | Comments on the PR |
| `reply` | `number`, `comment_id`, `body` | Replies to a review comment |
| `issue` | `title` (`body` optional) | Files an issue |

The run's name is `<bot> <action> <repo> <number>`, and its summary shows the URL of what it created:

```sh
gh run list -R Treedome-Technologies/.github -w bot.yml -L 1   # find the run
gh run watch <run-id> -R Treedome-Technologies/.github --exit-status
```

## Bot reports

SpongeBot welcomes first-time contributors, SquidBot reports the CI checks, and K.R.A.B.S. reports runner time and dependency changes. No AI runs; the reports are plain scripts. Each repo turns them on with this file, `.github/workflows/bot-reports.yml`:

```yaml
# Bot reports: the Treedome bots' scripted reports on pull requests, after ci finishes.
name: bot-reports

on:
  workflow_run:
    workflows: [ci]
    types: [completed]

permissions: {}

jobs:
  report:
    if: github.event.workflow_run.event == 'pull_request'
    permissions:
      actions: read
      pull-requests: read
    uses: Treedome-Technologies/.github/.github/workflows/bot-reports-shared.yml@main
    secrets: inherit
```

It runs from the repo's default branch after `ci` finishes, never from a pull request's own code, so the bots' keys (organization secrets) stay out of contributors' reach. A merged change to the shared workflow reaches every repo at once, so test it first by pointing scratch's caller at the pull request's branch.

## Guarantees

- Only the organization owner can run bot actions. No AI runs in any workflow here, and no AI credential is stored here.
- Each bot token is limited to one target repo and the permissions its action needs, and is revoked when the job ends.
- Bots never approve, never push (their apps have read-only contents), and never act on their own.

## Security

Report vulnerabilities privately through this repository's **Security** tab → **Report a vulnerability**.
