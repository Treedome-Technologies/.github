# Requirements

Keywords: **SHALL** (mandatory), **SHALL NOT** (prohibited), **SHOULD** / **SHOULD NOT** (recommended / discouraged), and **MAY** (optional), as in RFC 2119 and RFC 8174. They're normative only in uppercase; text without one is context, not a requirement.

Each row holds one requirement with one keyword, so it can be tested on its own. IDs are permanent: never reuse or renumber them, and mark a dropped requirement "Removed" instead of deleting its row.

## 1. Scope
- **Purpose:** organization-level files for Treedome Technologies: the "Meet the team" profile, and a shared workflow that posts the bots' scripted reports on pull requests.
- **Users:** visitors to the organization page; contributors whose pull requests get reports; the organization owner, who maintains both.
- **Out of scope:** anything driven by AI. AI reviews are triggered only by the organization owner, from `bot-actions`.

## 2. Functional requirements

### 2.1 Bot reports
*As a contributor, I want quick feedback from the team on my pull request, so I know where it stands.*

| ID | Requirement | Notes |
|---|---|---|
| REQ-001 | After a pull request's `ci` run finishes, SquidBot SHALL post a report listing each CI job with its result. | |
| REQ-002 | After a pull request's `ci` run finishes, K.R.A.B.S. SHALL post a report with the run's runner minutes, rounded up per job. | |
| REQ-003 | K.R.A.B.S.'s report SHALL list the dependency files the pull request changes. | |
| REQ-004 | SpongeBot SHALL post a welcome checklist when the pull request's author is a first-time contributor. | |
| REQ-005 | SpongeBot SHALL NOT post a welcome for any other author. | |
| REQ-006 | Each bot SHALL keep one report comment per pull request and update it on later runs instead of adding another. | |
| REQ-007 | The workflow SHALL report only on runs that finished with success, failure or a timeout. | Cancelled and waiting runs have no results |
| REQ-008 | The workflow SHALL skip runs triggered by Dependabot. | Its runs get no Actions secrets |
| REQ-009 | The workflow SHALL post nothing when no open pull request matches the run's commit. | |

## 3. Non-functional requirements
Every subsection below is always present. One with nothing to require says "None." and why.

### 3.1 Security
| ID | Requirement | Notes |
|---|---|---|
| REQ-010 | The workflow SHALL NOT check out or run pull request code. | |
| REQ-011 | Text from the pull request or its CI run SHALL reach shell steps only through environment variables or files, never through expression interpolation. | Script injection |
| REQ-012 | Posted text from the pull request or its CI run SHALL have mention, link, HTML, code-span and table characters removed, URLs broken, and length capped. | |
| REQ-013 | Each bot token SHALL be limited to the one repository and to pull request write and contents read permissions. | |
| REQ-014 | The workflow's own `GITHUB_TOKEN` SHALL have read-only access to Actions and pull requests only. | |
| REQ-015 | The workflow SHALL NOT run AI or use any AI credential. | |
| REQ-016 | Third-party actions SHALL be pinned to a full commit SHA. | |

### 3.2 Privacy
None. Reports restate public CI data and file names; nothing is stored or sent elsewhere.

### 3.3 Logging
None. Steps log only which reports they posted, and GitHub masks secrets in logs.

### 3.4 Reliability
| ID | Requirement | Notes |
|---|---|---|
| REQ-017 | A failed report SHALL NOT change the result of the repository's `ci` run. | Reports run as a separate workflow |

### 3.5 Performance
| ID | Requirement | Notes |
|---|---|---|
| REQ-018 | A report run SHALL time out after 5 minutes. | |

### 3.6 Compatibility
| ID | Requirement | Notes |
|---|---|---|
| REQ-019 | Within a major release, the shared workflow SHALL keep its file name and the secret and variable names it reads. | Callers pin a release |

### 3.7 Operations
| ID | Requirement | Notes |
|---|---|---|
| REQ-020 | A repository SHALL be able to turn reports on by adding one caller file, with no other setup. | Org secrets reach all repos |
