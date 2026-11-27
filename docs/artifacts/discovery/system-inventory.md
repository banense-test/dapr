## Document Control

Phase: Survey
Status: Draft — iteration 1; Documentation read in full, Perimeter at surface breadth, remaining sections recorded to the depth this iteration's slice allowed
Milestone Target: end-of-Survey milestone (SBC) — not achieved

## Perimeter
The system under reading is `dapr/dapr`: six Go executables that share one library, one set of protocol
contracts and one delivery surface. Its boundary is drawn by what the code reaches for, not by what a document
claims: the attached application, the Kubernetes API server, an embedded etcd, the configured component
backends, and the external services those backends and the conversation/search APIs call.

```plantuml
@startuml
title Perimeter of the existing system - DAPR Core, as the repository tree and go.mod declare it
skinparam componentStyle rectangle
skinparam packageStyle rectangle

package "DAPR Core (the delivered system)" {
  package "cmd/ - six executables" {
    [daprd sidecar runtime] as DAPRD
    [Sidecar injector] as INJ
    [Operator] as OPER
    [Placement] as PLACE
    [Scheduler] as SCHED
    [Sentry CA] as SENTRY
  }

  package "Protocol contracts" {
    [dapr/proto/ - 37 proto files] as PROTO
    [pkg/proto/ - generated Go bindings] as GENPROTO
  }

  package "Library the binaries link" {
    [pkg/ - runtime, api, actors,\nworkflow engine, components] as PKGAPI
  }

  package "Delivery surface" {
    [charts/dapr - 7 subcharts, 7 CRDs] as CHARTS
    [docker/ - 8 Dockerfiles] as DOCKER
  }
}

package "Outside the boundary" {
  [Application workload\n(the process daprd is attached to)] as APP
  [Kubernetes API server] as K8S
  [Embedded etcd\n(inside the scheduler)] as ETCD
  [Pluggable component backends:\nstate stores, brokers, secret stores,\nbindings, configuration, locks, search] as COMPS
  [Cloud provider APIs:\nAzure, AWS, GCP, Alibaba, Huawei,\nTencent, Oracle] as CLOUD
  [LLM and conversation providers:\nOpenAI, Anthropic, Bedrock, Ollama,\nGoogleAI, Mistral, HuggingFace] as LLM
  [OTel and Zipkin collectors] as OTEL
  [SPIFFE identities and mTLS peers] as SPIFFE
}

DAPRD --> PKGAPI
INJ --> PKGAPI
OPER --> PKGAPI
PLACE --> PKGAPI
SCHED --> PKGAPI
SENTRY --> PKGAPI
PKGAPI --> PROTO
PROTO --> GENPROTO

DAPRD --> APP : HTTP and gRPC app channel,\napp callback
DAPRD --> COMPS : component operations
DAPRD --> CLOUD
DAPRD --> LLM : conversation API
DAPRD --> OTEL : traces, metrics
DAPRD --> SPIFFE : mTLS via Sentry
DAPRD --> PLACE : actor placement stream
DAPRD --> SCHED : durable jobs and reminders
DAPRD --> SENTRY : certificate signing

INJ --> K8S : admission webhook,\npod patch
OPER --> K8S : CRD informers
CHARTS --> K8S : install and upgrade
SCHED --> ETCD : embedded durable state
SENTRY --> K8S : trust bundles, service-account tokens

note bottom of PROTO
  Every executable's contract is declared in dapr/proto/.
  The generated Go code under pkg/proto/ is committed,
  not built at release time.
end note
note bottom of APP
  The only actor the runtime serves: the application whose
  process the sidecar is attached to. Everything else in this
  diagram is a dependency the operator configures.
end note
@enduml
```

### Components

| Component | Kind | Responsibility | Implemented by |
|---|---|---|---|
| daprd (sidecar runtime) | Component | Serves every Dapr API to the attached application and hosts the component, actor, workflow, resiliency and mTLS machinery | `cmd/daprd/main.go`, `cmd/daprd/app/app.go`, `pkg/runtime/runtime.go` |
| In-tree component shims | Component | Registers the component implementations by category — state, pub/sub, bindings, secret stores, configuration, locks, crypto, name resolution, HTTP middleware, conversation, search, vector, binary store | `cmd/daprd/components/` (156 files), `pkg/components/*/registry.go` |
| HTTP API surface | Component | Routes and serves the `/v1.0` HTTP endpoints of the runtime | `pkg/api/http/server.go`, `pkg/api/http/endpoints/` |
| gRPC API surface | Component | Serves the public gRPC service, the internal service and the app-callback proxy | `pkg/api/grpc/server.go`, `pkg/api/grpc/endpoints.go`, `pkg/api/grpc/proxy/` |
| Universal API layer | Layer | Handler logic shared by the HTTP and gRPC surfaces so both expose one behaviour | `pkg/api/universal/` (29 files) |
| Actor runtime | Component | Virtual actor table, placement client, reminders and timers, reentrancy, actor state resolution | `pkg/actors/` (243 files) |
| Workflow engine | Component | Durable workflow and activity orchestration over the actor runtime, with history, retention, purge and rerun | `pkg/runtime/wfengine/` (62 files) |
| Operator | Component | Reconciles the Dapr custom resources into what a sidecar can consume; serves the operator gRPC service | `cmd/operator/`, `pkg/operator/` (38 files) |
| Placement | Component | Consistent-hash actor placement table, dissemination rounds, leadership over Raft | `cmd/placement/`, `pkg/placement/` (26 files) |
| Scheduler | Component | Durable job and actor-reminder scheduling on an embedded etcd; also serves placement during the cutover | `cmd/scheduler/`, `pkg/scheduler/` (76 files) |
| Sentry | Component | Certificate authority: workload SVID issuance, trust bundles, OIDC/JWKS validation | `cmd/sentry/`, `pkg/sentry/` (36 files) |
| Sidecar injector | Component | Kubernetes admission webhook that patches a pod to carry the daprd container | `cmd/injector/`, `pkg/injector/` (29 files) |
| Proto contracts | Layer | The proto3 definitions that declare every service and message of the system: 37 files in 8 packages | `dapr/proto/` (45 files) |
| Generated Go bindings | Layer | Committed protoc output: messages, gRPC stubs and Connect handlers | `pkg/proto/` (66 files) |
| CRD type definitions | Layer | Go types for the seven Dapr custom resources | `apis/` (46 files) |
| Helm charts | Component | Deployment of the components, RBAC and default configuration: 7 subcharts, 7 CRDs | `charts/dapr/` (67 files) |
| Container images | Component | Eight Dockerfiles covering Linux, Windows, debug and dev | `docker/` (22 files; `Dockerfile` plus the `-debug`, `-dev`, `-mariner` and four `-windows*` variants) |
| Shared library | Layer | Everything the six binaries link: api, actors, components, config, diagnostics, messaging, resiliency, security | `pkg/` (1 330 files) |
| Build and test tooling | Component | Lint, e2e and perf driver binaries that live beside the production executables | `cmd/check-lint-version.go`, `cmd/e2e.go`, `cmd/perf.go`, `cmd/root.go`, `cmd/zz-e2e-perf.go` |

### External actors and systems

| External actor or system | Reached through | Anchor |
|---|---|---|
| Application workload — the process the sidecar is attached to | Service — the app-channel HTTP/gRPC call in, and the app-callback service called out | `README.md`, § Why Dapr / Run Anywhere ("Dapr runs as a sidecar … next to your app"); `dapr/proto/runtime/v1/appcallback.proto` |
| Kubernetes API server | Remote call — informers, CRD reconcile, pod patch, resource and secret reads | `apis/*/v1alpha1/types.go`, `pkg/operator/api/informer/`, `pkg/injector/service/`, `charts/dapr/crds/` (7 CRDs) |
| Embedded etcd inside the scheduler | Data manager — the scheduler's own durable job store | `go.mod`, `go.etcd.io/etcd/server/v3 v3.5.33`; `pkg/scheduler/server/internal/etcd/etcd.go` |
| Configured component backends — state stores, message brokers, secret stores, bindings, configuration stores, lock stores, search and vector backends | Service — component operations dispatched by the runtime; the implementations are pluggable or come from `dapr/components-contrib` | `pkg/components/*/registry.go`, `pkg/runtime/compstore/`, `go.mod` (`github.com/dapr/components-contrib`) |
| Cloud provider APIs — Azure, AWS, GCP, Alibaba Cloud, Huawei Cloud, Tencent Cloud, Oracle Cloud | Remote call — through the component shims and cloud SDKs | `cmd/daprd/components/` (`state_azure_*`, `bindings_aws_*`, `secretstores_gcp_*`, `state_alicloud_*`, `secretstores_tencentcloud_ssm.go`, `state_oracledatabase.go`), `go.mod` (indirect cloud SDK blocks) |
| LLM and conversation providers — OpenAI, Anthropic, AWS Bedrock, Google AI, Mistral, HuggingFace, DeepSeek, Ollama | Remote call — the Conversation API and its component shims | `cmd/daprd/components/conversation_*.go` (9 files), `dapr/proto/runtime/v1/ai.proto` |
| OpenTelemetry and Zipkin collectors | Remote call — OTLP gRPC/HTTP and Zipkin exporters | `go.mod` (`go.opentelemetry.io/otel/exporters/...`), `pkg/diagnostics/tracing.go` |
| SPIFFE identities and mTLS peers | Service — workload SVIDs, trust bundles, peer verification | `pkg/security/spiffe/`, `dapr/proto/sentry/v1/sentry.proto`, `go.mod` (`github.com/spiffe/go-spiffe/v2`) |
| Helm chart repository and container registry | Remote call — install and upgrade paths named in the release notes | `docs/release_notes/template.md`, § Upgrading ("helm repo add dapr https://dapr.github.io/helm-charts/") |
| GitHub Actions | Messaging — the pipeline that builds, tests and releases the tree | `.github/workflows/` (25 files), `.github/scripts/` (82 files) |

### Entry points

| ID | Entry point | Kind | Initiated by | Anchor |
|---|---|---|---|---|
| EP-001 | daprd HTTP API — the `/v1.0` endpoint family | Service operation | Application workload and language SDKs calling the sidecar | `README.md`, § Distributed Application APIs ("All APIs are available over HTTP and gRPC"); `pkg/api/http/server.go`, `pkg/api/http/endpoints/` |
| EP-002 | daprd public gRPC API — the Dapr service | Service operation | Application workload and language SDKs | `dapr/proto/runtime/v1/dapr.proto`, `pkg/api/grpc/server.go` |
| EP-003 | App callback service — invoked into the application by daprd | Service operation | daprd, on a component event, an actor call, a job or a workflow task | `dapr/proto/runtime/v1/appcallback.proto`, `pkg/channel/` |
| EP-004 | Operator gRPC service | Service operation | daprd, on component, configuration and resiliency updates | `dapr/proto/operator/v1/operator.proto`, `pkg/operator/api/` |
| EP-005 | Placement gRPC service | Service operation | daprd actor runtime, on stream connect and placement-table dissemination | `dapr/proto/placement/v1/placement.proto`, `pkg/placement/internal/server/` |
| EP-006 | Scheduler gRPC service | Service operation | daprd, for durable jobs and actor reminders; placement during the cutover | `dapr/proto/scheduler/v1/scheduler.proto`, `pkg/scheduler/server/api.go` |
| EP-007 | Sentry gRPC service — certificate signing | Service operation | daprd, operator, placement, scheduler and injector | `dapr/proto/sentry/v1/sentry.proto`, `pkg/sentry/server/server.go` |
| EP-008 | Sidecar-injector admission webhook | Service operation | Kubernetes API server, on pod creation | `charts/dapr/charts/dapr_sidecar_injector/templates/dapr_sidecar_injector_webhook_config.yaml`, `pkg/injector/service/handler.go` |
| EP-009 | Pluggable component gRPC services — bindings, pub/sub, state, secret store | Service operation | daprd, when a component is declared as a pluggable one | `dapr/proto/components/v1/{bindings,pubsub,state,secretstore}.proto`, `pkg/components/pluggable/` |
| EP-010 | Internal service-invocation gRPC service | Service operation | A remote daprd, on a service-to-service call | `dapr/proto/internals/v1/service_invocation.proto`, `pkg/messaging/direct_messaging.go` |
| EP-011 | Health endpoints (`/healthz`) of every component | Service operation | Kubernetes probes and operators | `pkg/healthz/server/server.go`, `pkg/api/http/healthz.go`, `charts/dapr/charts/` (per-component probes) |
| EP-012 | Metrics endpoints (`/metrics`, `/metrics/scaling`) of every component | Service operation | Prometheus scrapers | `pkg/metrics/exporter.go`, `docs/development/dapr-metrics.md` |
| EP-013 | Operator and sidecar informers over the Dapr custom resources | Platform event | Kubernetes API server, on CRD add, update and delete | `dapr/proto/operator/v1/operator.proto` (informer messages), `pkg/operator/api/informer/`, `pkg/runtime/hotreload/loader/operator/` |
| EP-014 | Build and test tooling commands — lint-version check, e2e and perf drivers | Platform event | A maintainer or CI, through `make` and the workflow definitions | `cmd/check-lint-version.go`, `cmd/e2e.go`, `cmd/perf.go`, `cmd/root.go` |

## Technology and Size

| Area | Language | Platform and frameworks, with version | Files | Anchor |
|---|---|---|---|---|
| `.github` | YAML, Python, JavaScript, Shell | GitHub Actions; `dependabot.yml`; 25 workflow definitions; 82 helper scripts | 120 | `.github/` |
| `CODEOWNERS` | Text | — | 1 | `CODEOWNERS` |
| `CONTRIBUTING.md` | Markdown | — | 1 | `CONTRIBUTING.md` |
| `GOVERNANCE.md` | Markdown | — | 1 | `GOVERNANCE.md` |
| `LICENSE` | Text | Apache License 2.0 | 1 | `LICENSE` |
| `README.md` | Markdown | — | 1 | `README.md` |
| `RELEASE.md` | Markdown | — | 1 | `RELEASE.md` |
| `SECURITY.md` | Markdown | — | 1 | `SECURITY.md` |
| `cmd` | Go | Go 1.26.11; `dapr/components-contrib v1.19.1-0.20261126140908-2c9078d426b0`, `dapr/kit v0.19.1-0.20261030175054-be979ab98cc4`, `dapr/durabletask-go v0.16.1-0.20261109153237-98658f38d8cb` | 183 | `cmd/`; `go.mod` (go directive; first `require` block) |
| `cmd/daprd/components` | Go | One file per component family: state, pub/sub, bindings, secret stores, configuration, locks, crypto, name resolution, middleware, conversation, search, vector, binary store | 156 | `cmd/daprd/components/` |
| `dapr` | Protocol Buffers (proto3) | 37 `.proto` definitions in 8 packages; `protoc` (version disputed across three documents, see AN-003); `connectrpc.com/connect v1.19.1`, `google.golang.org/grpc v1.83.2` | 45 | `dapr/proto/`, `go.mod` |
| `docs` | Markdown | — | 195 | `docs/` |
| `docs/decision_records` | Markdown | 23 decision records in 5 categories plus an index | 24 | `docs/decision_records/` |
| `docs/release_notes` | Markdown, PNG | One note per release from `v0.1.0` to `v1.19.3`, plus a template and an assets folder | 153 | `docs/release_notes/` |
| `go.mod` | Go module manifest | `module github.com/dapr/dapr`; `go 1.26.11`; 10 direct SDK and 4 replacement rules | 1 | `go.mod` |
| `go.sum` | Go checksums | — | 1 | `go.sum` |
| `img` | PNG, SVG, JPEG | Banner, conceptual model, overview diagram | 3 | `img/` |
| `pkg/proto` | Go, generated | `protoc-gen-go`, `protoc-gen-go-grpc`, `protoc-gen-connect-go` output, committed rather than built at release time | 66 | `pkg/proto/` |

## Persistent Data

| Store | Kind | Data manager | Anchor |
|---|---|---|---|
| Seven Dapr custom resources — `Component`, `Configuration`, `Resiliency`, `Subscription`, `HTTPEndpoint`, `MCPServer`, `WorkflowAccessPolicy` | Structured document | Kubernetes API server, through `k8s.io/client-go v0.32.3` informers | `apes/*/v1alpha1/types.go` — `apis/{components,configuration,resiliency,subscriptions,httpEndpoint,mcpserver,workflowaccesspolicy}/v1alpha1/types.go`; `charts/dapr/crds/` (7 files) |
| Scheduler job store | Structured document | Embedded etcd server, `go.etcd.io/etcd/server/v3 v3.5.33` | `pkg/scheduler/server/internal/etcd/etcd.go`; `go.mod` |
| SQLite state store | Relational schema | `modernc.org/sqlite v1.42.2` through the state-store component | `cmd/daprd/components/state_sqlite.go`; `go.mod` |
| Local file secret store | Record file | Files read from a configured directory | `cmd/daprd/components/secretstores_local_file.go` |
| Local storage binding | Record file | Files written to a configured directory | `cmd/daprd/components/bindings_localstorage.go` |
| Actor and workflow state (the `actorStateStore` component) | Relational schema where the configured component is a SQL store; the store itself is outside the boundary | The configured state-store component, resolved at start-up | `pkg/runtime/actorstatestores.go`, `pkg/actors/state/resolver.go`, `pkg/runtime/wfengine/state/` |

## Documentation

What the delivery says about itself, graded against the tree. The owner's `.iari/README.md` names the stakeholder
and points to no document, so the perimeter documents were found from the tree and from each other.

```plantuml
@startuml
title Documentation the delivery carries, graded against the repository tree
skinparam componentStyle rectangle
skinparam wrapWidth 240

package "Current - nothing in the tree contradicts it" {
  [README.md\nsystem statement] as D1
  [docs/README.md\nin-tree docs index] as D2
  [docs/CONTRIBUTING.md\nproject contribution contract] as D3
  [CONTRIBUTING.md\nupstream contribution guide] as D4
  [GOVERNANCE.md\nmaintainer governance] as D5
  [CODEOWNERS\nreview ownership] as D6
  [LICENSE\nApache-2.0] as D7
  [docs/development/\nsetup, build, debug, metrics] as D8
  [docs/release_notes/\n153 files, v0.1.0 to v1.19.3] as D9
  [docs/release_notes/template.md] as D10
  [.github/pull_request_template.md] as D11
  [.github/ISSUE_TEMPLATE/\n9 forms] as D13
}

package "Contradicted by the code" {
  [docs/decision_records/decision_records.md\nindexes ARC-004, absent from the tree] as X1
  [dapr/README.md\npackage table omits scheduler/ and workflows/] as X2
  [tools/proto/generate.sh\nreads proto inputs the tree does not hold] as X3
  [dapr/proto/runtime/v1/README.md\nprotoc v4.25.4] as X4
  [.github/copilot-instructions.md\nprotoc v34.1] as X5
}

package "Outdated - true when written, not true now" {
  [docs/decision_records/**\nno record added since 2023-09-11] as O1
  [cmd/*/README.md\nsix three-line redirects to docs.dapr.io] as O2
  [RELEASE.md and SECURITY.md\none-line pointers into dapr/community] as O3
}

note bottom of X3
  generate.sh generates from pkg/proto/dapr/dapr.proto and
  pkg/proto/daprclient/daprclient.proto. The tree holds
  dapr/proto/runtime/v1/dapr.proto, and pkg/proto/ carries
  generated .pb.go files only.
end note
note bottom of X1
  The index lists 24 links across ARC, API, CLI,
  SDK and ENG. The tree holds 23 records:
  architecture/ has ARC-001 to ARC-003.
end note
@enduml
```

| Document | Useful for | How up to date | Anchor |
|---|---|---|---|
| `README.md` | What the system is, the twelve APIs it exposes, the platforms it runs on, the SDKs, the repository family | Current — every claim checked against the tree holds: six binaries, the API set, the sidecar model | `README.md`, § The runtime for durable execution / Distributed Application APIs / Run Anywhere |
| `docs/README.md` | The index of in-tree runtime documentation: three pointers — decision records, development, release notes | Current — the three it names all exist | `docs/README.md`, § Runtime docs |
| `docs/CONTRIBUTING.md` | This project's contract: the rules of the owner's guide it replaces, the label vocabulary, the branch rule, the conventions table, what a review checks | Current — every path it cites was checked and exists | `docs/CONTRIBUTING.md`, § 1 Rules of the existing guide this file replaces through § 5 What a review checks |
| `CONTRIBUTING.md` (root, the owner's) | The upstream contribution workflow: DCO sign-off, the four issue types, the pull-request sequence, third-party licensing, slash commands | Current — it is the owner's text, unchanged below the one pointer line added at its top with the stakeholder's approval | `CONTRIBUTING.md`, § Issues / Contributing to Dapr / Developer Certificate of Origin |
| `GOVERNANCE.md` | How the project is governed: maintainer responsibility, the three-month activity rule, the super-majority vote, the CODEOWNERS pull request | Current — the CODEOWNERS file it names exists and holds the two maintainer teams | `GOVERNANCE.md`, § Project Maintainers |
| `CODEOWNERS` | Who owns review of the whole tree | Current | `CODEOWNERS` |
| `LICENSE` | The licence grant: Apache 2.0, with the Dapr Authors copyright form | Current | `LICENSE` |
| `RELEASE.md` | The pointer to the release process | Current as a pointer — the process it names lives in `dapr/community`, outside the delivery | `RELEASE.md` |
| `SECURITY.md` | The pointer to the security-issue policy | Current as a pointer — the policy lives in `docs.dapr.io`, outside the delivery | `SECURITY.md`, § Security Policy |
| `docs/decision_records/decision_records.md` | The decision-record index, the five category prefixes, the naming rule and the required fields of a record | Contradicted by the code — it links `ARC-004-http-server.md`, and `docs/decision_records/architecture/` holds three records; the index counts 24 links against 23 files | `docs/decision_records/decision_records.md`, § Dapr decision record organization and index (Architecture list, last entry) |
| `docs/decision_records/**` — 23 records in ARC, API, CLI, SDK, ENG | The architecture, API, CLI, SDK and engineering decisions in force | Outdated — the newest record was committed on 2023-09-11 (`c338385`), while features landed through 2026-11 (scheduler placement authority `3d9dbe6`, search and vector blocks `04215fd`, workflow schedules `61914fd`); the only later commit touching the folder is a typo fix (`62d5128`, 2026-11-25) | `docs/decision_records/{architecture,api,cli,sdk,engineering}/`; commit log of the folder |
| `docs/development/README.md` and its five guides | How to set up a development environment, build, debug, run unit tests, and configure CI | Current — the commands it gives (`make build`, `make test`, `make check`, `make docker-deploy-k8s`, golangci-lint v2.10.1) match `.github/copilot-instructions.md` and the `Makefile` targets they name | `docs/development/README.md`; `docs/development/developing-dapr.md`, § Build the Dapr binaries / Run unit tests / One-line command for local development |
| `docs/development/dapr-metrics.md` | The metric catalogue per process and per component, and the tags each metric carries | Current — it marks the metrics retired in 1.17 as retired and names the source file of every group | `docs/development/dapr-metrics.md`, § Dapr Common metrics through § Job metrics |
| `docs/development/preview-features.md` | How a preview feature is gated: the flag is declared in `pkg/config/configuration.go`, toggled on a Configuration resource, and removed at GA | Current — the file and the `IsFeatureEnabled` call it names are the ones the tree holds | `docs/development/preview-features.md`, § Declaring / Toggling / Using |
| `docs/release_notes/` — 153 entries, `v0.1.0` to `v1.19.3` | What changed in each release: the problem, the impact, the root cause and the solution, per fix | Current — the newest notes (`v1.19.3`, `v1.18.12`, `v1.17.21`) accompany commits of 2026-11-26 | `docs/release_notes/`; `docs/release_notes/v1.19.3.md` |
| `docs/release_notes/template.md` | The shape of a release note and its substitution variables | Current — its sections are the ones the newest notes fill | `docs/release_notes/template.md`, § New in this release / Upgrading / Breaking Changes / Deprecation Notices |
| `.github/copilot-instructions.md` | Build, test, lint, formatting and convention guidance for a contributor or an agent: package layout, build tags, the licence header, import grouping, test patterns, the PR checklist | Contradicted by the code — the build, test and lint guidance matches the tree, but it states `protoc` v34.1 while `dapr/proto/runtime/v1/README.md` states v4.25.4, `dapr/README.md` states v25.4 and `tools/proto/generate.sh` pins 3.10.0 | `.github/copilot-instructions.md`, § Protobuf / gRPC (required tools) |
| `.github/pull_request_template.md` | The pull-request checklist a change is asked to satisfy | Current — every item is checkable against the tree (compile, tests, docs and spec issue in `dapr/docs`, sample) | `.github/pull_request_template.md`, § Checklist |
| `.github/ISSUE_TEMPLATE/` — 9 forms | The intake shape of a bug, a discussion, an endgame, a feature request, a minor patch release, a proposal, a question and a release planning item | Current | `.github/ISSUE_TEMPLATE/` |
| `cmd/daprd/README.md`, `cmd/injector/README.md`, `cmd/operator/README.md`, `cmd/placement/README.md`, `cmd/sentry/README.md` | A pointer to the published documentation of each executable | Outdated as documentation — each is a three-line redirect to `docs.dapr.io`, so no in-tree document states how any of the five behaves; `cmd/scheduler/` has no README at all | `cmd/daprd/README.md`; `cmd/injector/README.md`; `cmd/operator/README.md`; `cmd/placement/README.md`; `cmd/sentry/README.md` |
| `dapr/README.md` | The proto package overview and the client-generation steps | Contradicted by the code — the package table lists `common`, `internals`, `runtime`, `operator`, `placement`, `sentry`, `components`, and the tree also holds `scheduler/` and `workflows/`; it names `protoc` v25.4 | `dapr/README.md`, § Overview and § Proto client generation |
| `dapr/proto/**/README.md` — 7 package notes | Per-package proto notes and the generation prerequisite | Contradicted by the code for `runtime/v1` (protoc v4.25.4); the others state no version | `dapr/proto/runtime/v1/README.md`, § Proto client generation |
| `tools/proto/generate.sh` | How proto clients for the SDKs are generated, and which proto files are the inputs | Contradicted by the code — the `generate()` loop reads `${top_root}/pkg/proto/dapr/dapr.proto` and `${top_root}/pkg/proto/daprclient/daprclient.proto`; `pkg/proto/` holds 66 generated `.pb.go` files and no `.proto`, and the definitions live under `dapr/proto/`. It also pins protoc 3.10.0 and the Java gRPC plugin 1.24.1 | `tools/proto/generate.sh`, § generate() and § Download and install tools |
| `.iari/README.md` | Who leads the project | Current — stakeholder input, not part of the delivery | `.iari/README.md` |

## History

Each row states the commits listed and the period they cover. A reading is drawn only from what those commits
show being added or taken out — never from how the code looks today.

| Area | Commits read and period | What was removed or reworked | Reading | Commits |
|---|---|---|---|---|
| `cmd` | 25 commits, 2026-06-23 → 2026-11-17 | The placement-to-scheduler cutover ran as five numbered steps (add protos, add scheduler placement, helm and flags, daprd takes placement from the scheduler, authority detection with rollback); search and vector building blocks added; binary-store building block added; metrics listen address made configurable on sentry, operator and injector; scheduler made to exit when it cannot bind its port and to retry client creation instead of exiting | Unstable | `2707e0a`, `effad08`, `9000808`, `1275498`, `04215fd`, `ee35baa`, `d221275`, `180905c`, `9abc62a`, `397d859`, `ec2ac5f`, `8608664`, `ece6ec7`, `3d9dbe6`, `9aeab3b`, `0ba3c1b`, `e87872a`, `5e54d0c`, `f2d92bc`, `5aeedcb`, `b8216ba`, `35a729a`, `6365688`, `717d62f`, `b334e5f` |
| `dapr` (proto contracts) | 25 commits, 2026-04-20 → 2026-11-18 | Scheduler placement protos added; actor list/get timers added; the MCP proto types replaced with the upstream Go SDK's types; workflow access policy extended to all operations; global and per-name concurrency limits added; cross-app workflow operations added; pull dispatch of reminders to sidecars added | Unstable | `14c3158`, `f664c59`, `5573c4e`, `2dab9fd`, `effad08`, `04215fd`, `397d859`, `3759a59`, `1d282c9`, `92bbb0e`, `3d9dbe6`, `e2030a5`, `19e9dbd`, `cd72c15`, `8fd31d1`, `df9137c`, `a86c03b`, `d16e4de`, `6d8290e`, `88a04dd`, `1ebb858`, `1b4bef8`, `39c1ef7`, `c581fa8`, `c8463fc` |
| `.github` | 25 commits, 2026-10-13 → 2026-11-26 | CI reworked for duration and reliability rather than for new function: a dedicated integration-test build cache and more parallelism, more packages per KinD run, a required gate because of generated names, DCO fixed for the merge queue, backport auto-merge added, version-skew runs retargeted as the previous minor moved, MacOS runner versions bumped, the perf cluster moved to a new region | Unstable | `acde1d4`, `14eea71`, `c783324`, `79b189f`, `9d1b21a`, `75a49d9`, `41ac782`, `a872136`, `9000808`, `e92225b`, `a8e26cf`, `edff789`, `c5a9d80`, `903a585`, `24f5c3c`, `cbeb475`, `a24a198`, `7f1b470`, `a4b1184`, `66f70d0`, `04215fd`, `aed36f3`, `c2c0842`, `0d489b3`, `10d05f1` |
| `docs/decision_records` | 20 commits, 2020-04-16 → 2026-11-25 | Records added until 2023-09-11 and nothing after it; a mis-titled file corrected in 2023-08; one record renumbered from API-008 to API-009 in 2020 when the numbering collided; the only commit after 2023 is a typo fix in 2026-11 | Consolidating — dormant rather than settling | `62d5128`, `c338385`, `0904549`, `bac6ae6`, `855f09c`, `3ab2591`, `6299e15`, `f4cc6b8`, `47b8651`, `856d8cd`, `8f34ff0`, `71086c4`, `08b630d`, `9f4356c`, `39335d8`, `5f6e428`, `d325087`, `5295e0a`, `50988ac`, `904a928` |
| `docs/release_notes` | 20 commits, 2026-10-30 → 2026-11-26 | A run of security and correctness fixes each took its own patch note across 1.17, 1.18 and 1.19 lines: SQLite driver update, gRPC header cap and constant-time token compare, forged outbox messages, malformed encrypted state, ACL verb rules without metadata, brotli advisory, Redis pub/sub read timeout | Stable — one note per fix, no rework of older notes | `ffbf163`, `0337c32`, `298d459`, `cd15eb6`, `b7d4231`, `4313e79`, `12c6cbd`, `29f2d37`, `4433e2f`, `7ee17e2`, `f09a418`, `0be3f04`, `55312d1`, `c10b835`, `bd54d83`, `3342040`, `f2baba4`, `ee602ae`, `6eb861d`, `30eeca1` |
| `go.mod` | 20 commits, 2026-10-13 → 2026-11-26 | The Go toolchain moved three times (1.26.9 → 1.26.10 → 1.26.11); `components-contrib`, `kit` and `durabletask-go` were pinned to exact pseudo-versions after periods of tracking HEAD; OpenTelemetry, gorilla/websocket and lz4 bumped together for `govulncheck`; `modernc.org/sqlite` bumped to stop leaking read transactions; brotli bumped for an advisory | Unstable | `ffbf163`, `f1895d6`, `298d459`, `4313e79`, `61914fd`, `24bfe82`, `2dab9fd`, `dc63861`, `effad08`, `c10b835`, `1275498`, `cded45a`, `9dcb298`, `d23b217`, `02f0b3c`, `accd8c8`, `04215fd`, `7f85241`, `397d859`, `05080a2` |
| `docs` (whole directory) | 25 commits, 2026-11-16 → 2026-11-27 | Before 2026-11-27 the only entries are release notes, typo fixes and the 1.17/1.18/1.19 patch notes; the 2026-11-27 entries are this project's own artifact and guide commits and carry no claim about the system | — not read for the system; the burst is this process writing | `9ebb541`, `5b90dc4`, `b1fe75e`, `84d16e0`, `21da553`, `83002fe`, `131c28c`, `9bdd38e`, `c72f585`, `65471ae`, `ea9c027`, `afb2e0c`, `2a61738`, `79ede0e`, `da96edb`, `0e06a44`, `71bbf5b`, `ffbf163`, `0337c32`, `62d5128`, `298d459`, `cd15eb6`, `b7d4231`, `4313e79`, `12c6cbd` |
| Root documents — `README.md`, `CONTRIBUTING.md`, `GOVERNANCE.md`, `LICENSE`, `CODEOWNERS`, `RELEASE.md`, `SECURITY.md` | Not read | — | Not read — the next iteration's history pass reads them | — |
| `img` | Not read | — | Not read — binary assets | — |
| `pkg/proto` | Not read | — | Not read — generated code; its history is `dapr/proto`'s history | — |

## Anomalies

Each row is a risk the Project Manager carries into the Risk List under the same `AN-NNN` label.

| ID | Anomaly | Kind | Evidence | Likelihood | Impact |
|---|---|---|---|---|---|
| AN-001 | The decision-record index links a record the delivery does not hold: `ARC-004-http-server.md` is listed under Architecture, and `docs/decision_records/architecture/` holds three records. The index offers 24 links; the folder holds 23 records. A reader following the index reaches a 404. | Missing from the delivery | `docs/decision_records/decision_records.md`, § Dapr decision record organization and index (Architecture list, fourth entry) against `docs/decision_records/architecture/` (3 files: ARC-001, ARC-002, ARC-003) | likely | moderate |
| AN-002 | The proto package overview in `dapr/README.md` describes seven packages while the tree holds nine: `scheduler/` and `workflows/` are absent from the table. The two omitted packages carry the scheduler contract the actor-reminder cutover depends on and the MCP server contract. | Document contradicted by the code | `dapr/README.md`, § Overview (7 rows) against `dapr/proto/scheduler/v1/` and `dapr/proto/workflows/v1/` | likely | low |
| AN-003 | Four different `protoc` versions are stated in three files that each claim to be the generation path: `tools/proto/generate.sh` pins 3.10.0, `dapr/README.md` names v25.4, `dapr/proto/runtime/v1/README.md` names v4.25.4, and `.github/copilot-instructions.md` names v34.1. Regenerating the committed bindings from any one of them does not reproduce the others, and no file says which version produced the 66 files under `pkg/proto/`. | Document contradicted by the code | `tools/proto/generate.sh`, § `VERSION=3.10.0`; `dapr/README.md`, § Proto client generation; `dapr/proto/runtime/v1/README.md`, § Proto client generation; `.github/copilot-instructions.md`, § Protobuf / gRPC | likely | moderate |
| AN-004 | The documented client-generation script reads proto inputs the tree does not hold and writes into sibling repositories: its `generate()` loop reads `pkg/proto/dapr/dapr.proto` and `pkg/proto/daprclient/daprclient.proto`, while `pkg/proto/` holds 66 generated `.pb.go` files and no `.proto`, and the definitions live under `dapr/proto/`. It also generates the JS, Java, Python and .NET SDKs into `../<language>-sdk/`, which are not part of this delivery. | Document contradicted by the code | `tools/proto/generate.sh`, § generate() (the `for proto_file in ...` loop) and § Download and install tools | likely | moderate |
| AN-005 | No decision record has been added since 2023-09-11 (`c338385`) although decisions of the first rank landed after it: scheduler-hosted actor placement with a cutover and rollback (`3d9dbe6`, 2026-09-21), the search and vector building blocks (`04215fd`, 2026-10-20), MCP server resources, and workflow schedules (`61914fd`, 2026-11-11). The system's own statement of its architecture stops three years before its architecture did. | Missing from the delivery | Commit log of `docs/decision_records/` (last substantive commit `c338385`, 2023-09-11; last commit `62d5128`, 2026-11-25, a typo fix) against the `cmd` and `dapr` commit logs | likely | moderate |
| AN-006 | The release process and the security-issue policy are not in the delivery. `RELEASE.md` is a single link into `dapr/community` and `SECURITY.md` is a single link into `docs.dapr.io`, so how a release is cut and how a vulnerability is reported, triaged and disclosed cannot be read from the tree that produces the release. | Missing from the delivery | `RELEASE.md` (one line); `SECURITY.md`, § Security Policy (one link) | likely | moderate |
| AN-007 | Compatibility with older releases is kept by patching the old release's test tree rather than by adapting the current one: `.github/scripts/version-skew-test-patches/` holds 70 patch files across `release-1.12` to `release-1.18`, split between `dapr-sidecar-master` and `control-plane-master`. Each new release line adds a folder of patches that must keep applying. | Exceptional entity | `.github/scripts/version-skew-test-patches/e2e/` (18 files in 5 release folders) and `.../integration/` (52 files in 6 release folders) | likely | moderate |
| AN-008 | Two test files are disabled by renaming rather than deleted or fixed: `actors_test.go-TODO` and `wfengine_test.go-TODO` sit beside the suites they belong to. The rename removes them from the build silently and leaves no record of what they assert or why they stopped passing. | Suspicious coding style | `pkg/runtime/wfengine/backends/actors/actors_test.go-TODO`; `pkg/runtime/wfengine/wfengine_test.go-TODO` — tree evidence only; the package is read in iteration 3 | possible | moderate |
| AN-009 | One component shim is disabled by renaming: `bindings_kitex.go_disabled` sits among the 156 component registrations, so the Kitex binding is neither registered nor removed. A reader counting components from the directory counts one that does not build. | Suspicious coding style | `cmd/daprd/components/bindings_kitex.go_disabled` | possible | low |
| AN-010 | The test substrate is larger than the code it tests and is the delivery's weight centre: `tests/` holds 3 335 files against `pkg/`'s 1 330 and `cmd/`'s 183, and the integration suite alone is 2 073 files. Reading it is the phase's overrun risk; the cut assigns it to iteration 4. | Exceptional entity | Repository tree: `tests/` (3 335 files), `tests/integration/suite/` (2 073 files), `pkg/` (1 330 files), `cmd/` (183 files) | likely | moderate |
| AN-011 | No in-tree document says how any of the six executables behaves. Five of them carry a three-line README that redirects to `docs.dapr.io`; `cmd/scheduler/` carries none. Behaviour that a reader of this repository needs — what the scheduler's placement mode does, when the injector declines to inject, what placement's leadership does — is only in a documentation site outside the delivery. | Missing from the delivery | `cmd/daprd/README.md`, `cmd/injector/README.md`, `cmd/operator/README.md`, `cmd/placement/README.md`, `cmd/sentry/README.md` (each a redirect); `cmd/scheduler/` (no README) | likely | moderate |

## Priorities

| Order | Area | Why it comes first | Anchor |
|---|---|---|---|
| 1 | `dapr/proto/` and `pkg/proto/` | The contracts declare every operation the system offers; every entry point this inventory labelled was found there, and the Use-Case Model recovers its use cases from these entry points. Reading them first makes every later area legible as an implementation of a declared contract. | `dapr/proto/runtime/v1/` (19 files), `dapr/proto/` (45 files), `pkg/proto/` (66 files) |
| 2 | `cmd/daprd/` | The primary executable and the place the 156 component shims are registered: it fixes what the sidecar can do and answers which components exist at all. | `cmd/daprd/` (161 files; 156 in `components/`) |
| 3 | `pkg/runtime/` | The largest library subtree (308 files) and the one the others bind together: hot reload, compstore, the component processor, the scheduler client and `wfengine/`. Every control plane's effect arrives here. | `pkg/runtime/` (308 files) |
| 4 | `pkg/api/` | The request path itself — HTTP, gRPC and the universal layer that both share. Its behaviour is what EP-001 and EP-002 actually are. | `pkg/api/` (124 files) |
| 5 | `pkg/actors/` | The stateful abstraction with its own table, placement client, reminders and workflow targets (243 files); the placement and scheduler cutover is argued over the semantics of this package. | `pkg/actors/` (243 files) |
| 6 | `pkg/scheduler/` | A separate process with its own embedded etcd, leadership and concurrency gates (76 files), and the newest control plane: it took the reminder and job authority from placement and the sidecar across five numbered commits. | `pkg/scheduler/` (76 files); commits `0ba3c1b`, `9aeab3b`, `3d9dbe6` |
| 7 | `pkg/placement/` | The older control plane it replaced, still carrying Raft leadership and dissemination (26 files); the cutover cannot be read without it. | `pkg/placement/` (26 files) |
| 8 | `pkg/sentry/` and `pkg/security/` | The trust domain every other component depends on to talk at all: SVID issuance, trust bundles, JWT and OIDC validation. | `pkg/sentry/` (36 files), `pkg/security/` (13 files) |
| 9 | `pkg/operator/`, `pkg/injector/`, `apis/`, `client/` | How a Dapr resource becomes a running sidecar: the reconcile loop, the admission patch and the CRD type surface. | `pkg/operator/` (38 files), `pkg/injector/` (29 files), `apis/` (46 files), `client/` (41 files) |
| 10 | `.github/workflows/` | The gates the delivery is held to — 25 workflows naming the build, test, version-skew, e2e and release steps. Unread this iteration, and it is where the tree's own quality claims are either kept or not. | `.github/workflows/` (25 files), `.github/scripts/` (82 files) |
| 11 | `charts/` and `docker/` | The delivery surface: seven subcharts and nine Dockerfiles are what actually ships, and the Deployment Model's trigger has already fired on them. | `charts/dapr/` (67 files), `docker/` (22 files) |
| 12 | `tests/` | The only empirical statement of behaviour in the tree, and its largest area (3 335 files). It is read last because it is read against the systems under test. | `tests/` (3 335 files) |
| 13 | The helper layer — `internal/`, `utils/`, `pkg/{backoff,buildinfo,encryption,expr,messages,outbox,responsewriter,retry,scopes,sse,validation}` | These expose no entry point of their own; they are legible only once their callers are known. | `internal/` (32 files), `utils/` (9 files), the eleven small `pkg/` packages |

## Coverage

Files opened against files in the tree, per area of this iteration's slice. An area at zero is declared unread, not
skimmed.

| Area | Files | Read | Unread and why |
|---|---|---|---|
| `.github` | 120 | 2 — `copilot-instructions.md`, `pull_request_template.md` | 118 unread: the 25 workflows and 82 scripts are code that states behaviour, not documents about it; they are priority 10 and belong to a later iteration. The 9 issue templates were read only as a directory listing. |
| `CODEOWNERS` | 1 | 1 — whole file | — |
| `CONTRIBUTING.md` | 1 | 1 — whole file | — |
| `GOVERNANCE.md` | 1 | 1 — whole file | — |
| `LICENSE` | 1 | 1 — whole file | — |
| `README.md` | 1 | 1 — whole file | — |
| `RELEASE.md` | 1 | 1 — whole file | — |
| `SECURITY.md` | 1 | 1 — whole file | — |
| `cmd` | 183 | 6 — the five component READMEs and the absence of a sixth checked against the tree | 177 unread: no `main.go`, `app.go` or `options.go` body was opened, and none of the 156 component shims. The entry points above are recorded at the surface the tree and the READMEs name; naming their operations is the next iteration's work. |
| `dapr` | 45 | 2 — `dapr/README.md`, `dapr/proto/runtime/v1/README.md` | 43 unread: the 37 `.proto` files and the other six package READMEs. The API surface is recorded from the package layout and the READMEs, not from the service definitions themselves. Priority 1. |
| `docs` | 195 | 9 — `README.md`, `decision_records/decision_records.md`, `CONTRIBUTING.md`, `development/README.md`, `development/developing-dapr.md`, `development/dapr-metrics.md`, `development/preview-features.md`, `release_notes/template.md`, `release_notes/v1.19.3.md` | 186 unread: the 23 decision-record bodies, 152 further release notes, the four remaining development guides, and `artifacts/` (this process's own files, not part of the system). The decision-record bodies are architecture evidence for Excavation. |
| `go.mod` | 1 | 1 — whole file | — |
| `go.sum` | 1 | 0 | Unread: the resolved checksum list restates the versions `go.mod` pins; it is read when a dependency question is actually open. |
| `img` | 3 | 0 | Unread: binary assets (`dapr_logo.svg`, `overview.png`, `dapr_conceptual_model.jpg`). The overview image is referenced by `README.md`; its content states nothing the text does not. |
| `pkg/proto` | 66 | 0 | Unread: committed generated code. Its contract is `dapr/proto/`, read at priority 1; the generated bodies are read only where a handler's behaviour is in question. |

Areas outside this iteration's slice — `pkg/` (1 330 files), `tests/` (3 335), `charts/` (67), `docker/` (22),
`internal/` (32), `utils/` (9), `apis/` (46), `client/` (41), `swagger/` (1), `grafana/` (6), `platforms/` (13),
`tools/` (4), `.build-tools/` (14), `.devcontainer/` (1), `Makefile`, `.golangci.yml`, `.prettierrc.json`,
`.codecov.yaml`, `.gitattributes`, `.gitignore`, `.prettierignore` — are not read this iteration. Where this
inventory names one of them it is as the implementer of a component or the anchor of a finding, never as a read area.

**Count of this iteration:** 28 files opened of the 619 files the fifteen slice areas hold; the tree holds 6 627
entries. The slice's documents are read in full; its code areas are read at surface breadth only.

## Traceability

| Element | Traces From | Link Type | Traces To |
|---|---|---|---|
| EP-001 | — | — | — |
| EP-002 | — | — | — |
| EP-003 | — | — | — |
| EP-004 | — | — | — |
| EP-005 | — | — | — |
| EP-006 | — | — | — |
| EP-007 | — | — | — |
| EP-008 | — | — | — |
| EP-009 | — | — | — |
| EP-010 | — | — | — |
| EP-011 | — | — | — |
| EP-012 | — | — | — |
| EP-013 | — | — | — |
| EP-014 | — | — | — |
| AN-001 | — | — | — |
| AN-002 | — | — | — |
| AN-003 | — | — | — |
| AN-004 | — | — | — |
| AN-005 | — | — | — |
| AN-006 | — | — | — |
| AN-007 | — | — | — |
| AN-008 | — | — | — |
| AN-009 | — | — | — |
| AN-010 | — | — | — |
| AN-011 | — | — | — |
