# .github

Organization-level files for Treedome Technologies. [`profile/README.md`](profile/README.md) is the "Meet the team" page shown on the [organization's overview](https://github.com/Treedome-Technologies).

Design docs: [requirements](docs/requirements.md), [architecture](docs/architecture.md) and [test plan](docs/test-plan.md).

CI here runs the baseline checks from [`nathanzm/.github`](https://github.com/nathanzm/.github), which holds the shared workflows for these repos, plus actionlint.

## Bot reports

[`bot-reports-shared.yml`](.github/workflows/bot-reports-shared.yml) posts the bots' scripted reports on pull requests: SpongeBot welcomes first-time contributors, SquidBot reports the CI checks, and K.R.A.B.S. reports runner time and dependency changes. No AI runs; the reports are plain scripts. Each repo turns them on with this file, `.github/workflows/bot-reports.yml`, pinned to a release of this repo:

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
    uses: Treedome-Technologies/.github/.github/workflows/bot-reports-shared.yml@<commit-sha> # v1.0.0
    secrets: inherit
```

It runs from the repo's default branch after `ci` finishes, never from a pull request's own code, so the bots' keys (organization secrets) stay out of contributors' reach.

Releases are semver tags (`vX.Y.Z`); callers pin the tag's commit SHA, and Dependabot proposes updates from the tags.
