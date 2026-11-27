# Contributing to DAPR Core

## Working with IARI

For how a change is requested, labelled and reviewed in this repository, this file holds over any other guide in it.

This repository also carries `CONTRIBUTING.md` at its root, written by the project owner. It describes how the upstream open-source project takes contributions. It stays as its author wrote it, and it remains the authority on the Developer Certificate of Origin, the code of conduct, third-party licensing and the upstream issue forms. Where its rules for **how a change is requested, how it is labelled and how it is reviewed** differ from how this project works, the rule below replaces it.

### 1. Rules of the existing guide this file replaces

Each row quotes the rule as the owner wrote it, names the file it is in, and states what holds here.

| Rule as written | Where it is | What holds for this project |
|---|---|---|
| "Make sure there's an issue (bug or proposal) raised, which sets the expectations for the contribution you are about to make." | `CONTRIBUTING.md`, section *Contributing to Dapr* > *Pull Requests* | A change is requested as an issue labelled `change-request`. The Change Control Manager owns its state through the `cr:*` labels; nothing is worked before it is `cr:approved`. |
| "There are 4 types of issues: Issue/Bug, Issue/Discussion, Issue/Proposal, Issue/Question." | `CONTRIBUTING.md`, section *Issues* > *Issue Types* | The nature of a change is carried by the `nature:*` labels — `nature:defect`, `nature:enhancement`, `nature:new-feature`, `nature:refactoring` — declared by whoever raises it. The upstream issue forms under `.github/ISSUE_TEMPLATE/` are not the intake path of this project. |
| "Before you create a new issue, please do a search in open issues to see if the issue or feature request has already been filed." | `CONTRIBUTING.md`, section *Issues* > *Before You File* | Kept in spirit, with a different mechanism: the Change Control Manager reconciles a new change request against the open ledger and closes a duplicate as such. Search is not the contributor's duty alone. |
| "add a \"[WIP]\" prefix in your PR's title and assign the **do-not-merge** label" | `CONTRIBUTING.md`, section *Use work-in-progress PRs for early feedback* | There is no `do-not-merge` label and no title prefix here. Work handed off for code review carries `ready-for-review`; the Implementer raises it, and the Code Reviewer, Integrator and Reviewer read it. |
| "A maintainer of the project will be assigned, and you can expect a review within a few days." | `CONTRIBUTING.md`, section *Contributing to Dapr* > *Pull Requests* | An approved change request carries `assigned:<role-kebab>` naming the role that executes it. Review is a recorded activity, not a waiting period: each defect becomes a finding with its criterion and severity, and the review approves by default once no Critical or Major finding stands. |
| "Wait for the CI process to finish and make sure all checks are green." | `CONTRIBUTING.md`, section *Contributing to Dapr* > *Pull Requests* | Kept, and made explicit as a gate: the Configuration Manager marks a failed gate `missing-approval`, `ci-broken-on-main` or `release-blocker`, and the Integrator marks a red integration line `integration-regression`. |
| "Fork the relevant repo and create a new branch" | `CONTRIBUTING.md`, section *Contributing to Dapr* > *Pull Requests* | Work happens on a branch of this repository, not a fork. Branch names follow the rule in section 3 below; a name that breaks it is marked `naming-violation`. |
| "Update relevant documentation for the change" | `CONTRIBUTING.md`, section *Contributing to Dapr* > *Pull Requests* | Kept, and extended: an artifact the change invalidates is updated in the same change, because a superseded artifact is read by the next role as current. |

Two rules of the owner's guide are kept without change and are read from `CONTRIBUTING.md` itself: every commit carries the DCO `Signed-off-by` line, and third-party code is included only with its license.

### 2. Requesting a change, and its labels

A change is requested by opening an issue and labelling it. The Change Control Manager moves it through its states; the role named by `assigned:<role-kebab>` executes it once it is approved. A release-blocking priority is the Change Control Manager's judgement of urgency, never a consequence of the severity the reporter declared — the two axes are independent and both are labelled.

| Label | Means | Raised by | Read by |
|---|---|---|---|
| `change-request` | The issue is a change request. Every change request carries it. | Change Control Manager, Tester | Capsule Designer, Database Designer, Designer, Implementer, Process Engineer, Reviewer, Test Manager, Tester, Change Control Manager, Software Architect |
| `cr:new` | State of a change request: new. | Change Control Manager | nobody |
| `cr:logged` | State of a change request: logged, waiting for triage and for the decision. | Change Control Manager, Tester | Change Control Manager |
| `cr:approved` | State of a change request: approved. The role its `assigned:` label names executes it. | Change Control Manager | Capsule Designer, Database Designer, Designer, Implementer, Process Engineer, Change Control Manager |
| `cr:assigned` | State of a change request: assigned. | Change Control Manager | nobody |
| `cr:complete` | State of a change request: complete. The correction is verified and the issue is closed. | Change Control Manager | nobody |
| `cr:rejected` | State of a change request: rejected. The issue is closed. | Change Control Manager | nobody |
| `cr:hold` | State of a change request: on hold. | Change Control Manager | nobody |
| `cr:deferred-next-iteration` | State of a change request: deferred to the next iteration. | Change Control Manager | nobody |
| `cr:deferred-next-release` | State of a change request: deferred to the next release. The issue is closed. | Change Control Manager | nobody |
| `assigned:<role-kebab>` | The role that executes an approved change request, its name in kebab-case. | Change Control Manager | Capsule Designer, Database Designer, Designer, Implementer, Process Engineer |
| `severity:blocker` | Fatal: the product, its build or its integration cannot be used. | Tester, Configuration Manager, Integrator, Change Control Manager | Configuration Manager, Integrator |
| `severity:major` | A major function is not performed. | Tester, Change Control Manager | nobody |
| `severity:minor` | The function works; a minor annoyance. | Tester, Configuration Manager, Change Control Manager | nobody |
| `severity:trivial` | Cosmetic: no function is affected. | Tester, Change Control Manager | nobody |
| `priority:critical` | Urgent (resolve immediately). Holds the release. | Change Control Manager | Change Control Manager, Reviewer, Tester |
| `priority:high` | High. Holds the release. | Change Control Manager | Change Control Manager, Reviewer, Tester |
| `priority:medium` | Normal. Does not hold the release. | Change Control Manager | Change Control Manager |
| `priority:low` | Low. Does not hold the release. | Change Control Manager | Change Control Manager |
| `nature:defect` | Nature of the change: a defect. | Tester, Configuration Manager, Integrator, Change Control Manager, IARI | Reviewer, Test Manager, Tester, Implementer |
| `nature:enhancement` | Nature of the change: an enhancement. | Integrator, Change Control Manager | nobody |
| `nature:new-feature` | Nature of the change: a new feature. | Change Control Manager | nobody |
| `nature:refactoring` | Nature of the change: a refactoring. | Change Control Manager | nobody |
| `impact:architectural` | Impact of the change: architectural. The Software Architect decides on it before it is approved. | Change Control Manager | Change Control Manager |
| `impact:local` | Impact of the change: local. | Change Control Manager | nobody |
| `impact:cross-cutting` | Impact of the change: cross-cutting. | Change Control Manager | nobody |
| `needs-architect-review` | The change request is parked until the Software Architect decides on it. | Change Control Manager | Software Architect, Change Control Manager |
| `architect:concurs` | The Software Architect concurs with the change request parked for its decision. | Software Architect | Change Control Manager |
| `architect:rejects` | The Software Architect rejects the change request parked for its decision. | Software Architect | Change Control Manager |
| `ready-for-review` | On a branch: its work is handed off for code review. | Implementer | Code Reviewer, Integrator, Reviewer |
| `handoff:release-notes` | The issue is the release summary of an iteration, handed off for the Release Notes and the release tag. | Integrator | Configuration Manager |
| `iteration-close` | The issue is the closing summary of an iteration. | Integrator | nobody |
| `missing-approval` | Gate failure: the review has not approved. | Configuration Manager | nobody |
| `ci-broken-on-main` | Gate failure: the pipeline is red on the main branch. | Configuration Manager | nobody |
| `release-blocker` | The gate failure blocks the release baseline. | Configuration Manager | nobody |
| `naming-violation` | A branch name breaks the naming convention. | Configuration Manager | nobody |
| `integration-regression` | A merge left the integration line or the main branch red. | Integrator | nobody |
| `integration-blocked` | A pull request the Integrator could not merge. | Integrator | nobody |
| `missing-dependency` | An artifact the integration depends on is missing. | Integrator | nobody |
| `production-break` | A merged hotfix broke the main branch after the merge. | Integrator | nobody |
| `self-audit` | IARI's self-audit found that a role broke a process invariant. | IARI | IARI |
| `process-defect` | A defect of the process: a role broke one of its invariants. | IARI | nobody |

A label written differently from this table reaches no role. Adding one is a change to this file, made by the Process Engineer.

### 3. Branching and deployment

- Every change is worked on a branch of this repository, never a fork. A branch is named `<type>/<slug>`:
  - `<type>` is one of `feature`, `fix`, `refactor`, `docs`, `chore`, `hotfix`.
  - `<slug>` is lower-case kebab-case words naming the change, at most 60 characters, no trailing slash.
  - Example: `fix/actor-reminder-expiry`, `docs/contribution-guide`.
  - The Configuration Manager marks a name that breaks this rule `naming-violation`; the Integrator does not merge the pull request until it is renamed.
- Branch and release procedure beyond that is the owner's and is stated in `RELEASE.md`, `.github/workflows/create-release.yaml`, `.github/workflows/backport.yaml` and `.github/scripts/create-release.sh`. Read those files for the procedure; this file does not restate it.
- Release notes follow `docs/release_notes/template.md`, taken as written.
- The pipeline configuration under `.github/workflows/` belongs to the Integrator, which reports what it runs and whether it is green. This file names the path, not its contents.

### 4. Code and test conventions

The conventions below are the ones the existing code already follows. Each is taken as written from the file that states it; this file cites them and does not paraphrase them, because a paraphrase drifts and the file does not.

| Convention area | File to read | What it settles |
|---|---|---|
| Go lint and formatting | `.golangci.yml` | The enabled linter set and its settings; the banned imports (for example `github.com/Sirupsen/logrus` in favour of `github.com/dapr/kit/logger`, `github.com/pkg/errors` in favour of the standard `errors` package, `gopkg.in/yaml.v2` in favour of `gopkg.in/yaml.v3`, `github.com/golang-jwt/jwt*` in favour of `github.com/lestrrat-go/jwx/v2`); the formatters `gofmt`, `gofumpt`, `goimports` with `github.com/dapr/` as the local prefix; the generated-code exclusions. Linting runs against the `unit`, `allcomponents` and `subtlecrypto` build tags. |
| JavaScript and JSON formatting | `.prettierrc.json` | `trailingComma: "es5"`, `tabWidth: 4`, `semi: false`, `singleQuote: true`. |
| Build, test and code generation | `Makefile`, `tests/dapr_tests.mk`, `docker/docker.mk`, `tools/codegen.mk` | The targets a change is verified with before it is handed off. `CONTRIBUTING.md` names `make lint` for Go and prettier for JavaScript and JSON. |
| Tests are part of the change | `CONTRIBUTING.md`, section *Contributing to Dapr* > *Pull Requests* | "Code changes require tests." Every change that alters behaviour carries the test that proves it. |
| Third-party code | `CONTRIBUTING.md`, section *Use of Third-party code* | "Third-party code must include licenses." |
| Commit sign-off | `CONTRIBUTING.md`, section *Developer Certificate of Origin* | Every commit carries a `Signed-off-by` line; `git commit -s` appends it. A pull request whose commits lack it is not merged. |
| Proto generation | `tools/proto/generate.sh`, `dapr/proto/**/*.proto` | How the proto definitions under `dapr/proto/` produce the generated code under `pkg/proto/`. Generated output is excluded from lint and formatting. |
| Test layout and how to write a test | `tests/docs/writing-integration-test.md`, `tests/docs/writing-e2e-test.md`, `tests/docs/writing-perf-test.md`, `tests/docs/running-e2e-test.md`, `tests/docs/running-perf-tests.md` | Where a test belongs, how it is structured, and how it is run. Integration tests live under `tests/integration/`, end-to-end under `tests/e2e/`, performance under `tests/perf/`. |
| Development environment | `docs/development/setup-dapr-development-env.md`, `docs/development/setup-dapr-development-using-vscode.md`, `.devcontainer/devcontainer.json`, `docker/Dockerfile-dev` | How a contributor gets a working build, in a container or on the host. |
| Preview features | `docs/development/preview-features.md` | How a feature that is not yet generally available is gated. |
| Architectural decisions | `docs/decision_records/` | The accepted decision records (`API-*`, `ARC-*`, `CLI-*`, `ENG-*`, `SDK-*`) a change must not silently contradict. A change that contradicts one raises a change request with `impact:architectural` and is parked for the Software Architect. |
| Toolchain versions | `go.mod`, `go.sum` | The Go version the module targets and the pinned dependency versions. Versions are not moved by preference; the version policy stated in the Development Case governs. |

### 5. What a review checks

A review is not an opinion about style. It checks, in this order:

1. **Trace.** The change traces to a declared requirement, a recovered entry point, or an approved change request. Something that traces to nothing is a scope question for the stakeholder, not a merge candidate.
2. **Artifact consistency.** Every artifact the change invalidates is updated in the same change. An artifact left stating the old truth is read by the next role as the current one.
3. **Conventions above.** The change passes what `.golangci.yml` and `.prettierrc.json` settle, and carries the test that proves it.
4. **No contradiction of an accepted decision record,** unless the change request carries `impact:architectural` and the Software Architect has concurred with `architect:concurs`.
5. **Findings recorded, not remembered.** A defect found in review becomes a finding with its criterion and severity. A Critical finding escalates to the stakeholder; a Major one is fixed before the artifact is consumed again; a Minor is fixed in its owner's next turn and does not hold the milestone.

A review closes a finding only through the lens that opened it. A change approved by a review whose gate failures are unmarked (`missing-approval`, `ci-broken-on-main`, `release-blocker`) is not approved.
