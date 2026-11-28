# Test plan

## Strategy
- **Static:** actionlint (with shellcheck) checks the workflows in CI on every pull request.
- **Integration:** real runs on `Treedome-Technologies/scratch`, whose caller is pinned to the commit under test: a test pull request that passes, then one that fails a check.
- **Local:** the report-writing step runs against sample data, including hostile job and file names.
- **Review:** properties that can't be triggered safely, such as runs from forks or Dependabot, are checked by reading the workflow.

## Test cases
Sections match [requirements.md](requirements.md).

### 2. Functional requirements
| Requirement | Test | Kind |
|---|---|---|
| REQ-001 | Test PR on scratch: SquidBot's comment lists every `ci` job and its result | Integration |
| REQ-002 | Same run: K.R.A.B.S.'s comment shows runner minutes | Integration |
| REQ-003 | Test PR that changes `requirements-dev.txt`: K.R.A.B.S. lists it | Integration |
| REQ-004 | Review: the welcome is gated on `FIRST_TIME_CONTRIBUTOR` or `FIRST_TIMER` (needs a second account to run) | Review |
| REQ-005 | Test PR by the organization owner or a bot: no welcome | Integration |
| REQ-006 | Push a second commit to the test PR: each bot's comment is edited, no new one | Integration |
| REQ-007 | Review: the job's `if:` allows only success, failure and timed_out | Review |
| REQ-008 | Review: the job's `if:` excludes `dependabot[bot]` | Review |
| REQ-009 | Push to a branch with no open PR: the run finds none and posts nothing | Integration |
| REQ-010 | Dispatch each action on scratch: the item appears under the bot's name | Integration |
| REQ-011 | The same runs: the summary shows each item's URL | Integration |

### 3. Non-functional requirements
| Requirement | Test | Kind |
|---|---|---|
| REQ-012 | Review: no `actions/checkout` or execution of PR files | Review |
| REQ-013 | actionlint's expression-injection check in CI | Static |
| REQ-014 | Local run with hostile job and file names: mentions, links, HTML and URLs come out inert | Local |
| REQ-015 | Review: each mint step sets `repositories` to the one repo and only the two permissions | Review |
| REQ-016 | Review: the job's `permissions` block | Review |
| REQ-017 | Review: no AI step or credential | Review |
| REQ-018 | Review: every `uses:` pins a full SHA | Review |
| REQ-019 | Review: the actor check runs before any mint step (a negative run needs a second account) | Review |
| REQ-020 | Dispatch with `repo=../x`, and a `review` with `number=abc`: each run fails before minting | Integration |
| REQ-021 | Review: each mint step sets `repositories` to the target repo and only that action's permissions | Review |
| REQ-022 | Review: `event=COMMENT` is the only event sent to the reviews endpoint | Review |
| REQ-023 | actionlint's expression-injection check in CI | Static |
| REQ-024 | CodeQL's excessive-secrets-exposure check on every pull request (no alerts) | Static |
| REQ-025 | Test PR: a failing report run leaves the `ci` result unchanged | Integration |
| REQ-026 | Review: `timeout-minutes: 5` | Review |
| REQ-027 | Removed | |
| REQ-028 | Review of every change: file, job, secret and variable names unchanged | Review |
| REQ-029 | Adding the caller file to scratch is the only setup the tests need | Integration |
