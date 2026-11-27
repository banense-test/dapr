## Document Control
Phase: Survey
Status: Draft — iteration 2. Perimeter closed against the code: the Components, External actors and systems, and Entry points tables are written, and the paragraph introducing them carries the only statement of their row counts. Technology and Size covers every top-level area of the tree, with the parent-and-child areas under `cmd`, `dapr`, `docs` and `pkg` listed separately. Persistent Data, Documentation, History, Anomalies and Priorities are carried forward from the reads of both iterations, with iteration 2's slice added; Anomalies states 23, each with its evidence and its likelihood and impact. Coverage states iteration 1's read of 13 assigned areas and iteration 2's read of the 16 of its slice — 86 distinct files of the 6 631 entries the tree holds — each count reproducible from the files enumerated on its own row.
Milestone Target: end-of-Survey (SBC) — not achieved
## Perimeter
The boundary is drawn by what the code reaches for. Six Go executables run from `cmd/` and share one library;
each exposes its own listeners, and the sidecar alone opens seven: the HTTP API port, a separate public port,
a pprof port when profiling is on, the gRPC API port, the internal gRPC port, the `/healthz` port and the
metrics port. `pkg/api/grpc/server.go` builds **two** gRPC servers from one file with different trust. The
three control planes reach the Kubernetes API server and each other over signed gRPC; everything a component
does beyond the boundary goes through a shim in `cmd/daprd/components/` to a backend the operator configured.

```plantuml
@startuml
title Perimeter of DAPR Core - the components, the boundary, and what the system reaches
skinparam componentStyle rectangle
skinparam wrapWidth 210
skinparam nodesep 16
skinparam ranksep 26

package "DAPR Core - inside the boundary" {
  package "Executables - cmd/" {
    [daprd\nthe sidecar] as DAPRD
    [operator] as OPER
    [injector] as INJ
    [placement] as PLACE
    [scheduler] as SCHED
    [sentry] as SENTRY
    [build-tools] as BT
  }

  package "Request path - pkg/api" {
    [HTTP API] as APIH
    [gRPC API] as APIG
    [internal ServiceInvocation] as APIINT
    [universal handlers] as UNIV
    [public API port] as APIPUB
  }

  package "Runtime core - pkg/runtime" {
    [compstore] as COMPSTORE
    [processor] as PROC
    [hot reload] as HOT
    [workflow engine] as WF
    [scheduler client] as SCHCL
  }

  package "Components plane" {
    [component registries\npkg/components] as REG
    [component shims\ncmd/daprd/components] as SHIM
    [pluggable components] as PLUG
  }

  package "Cross-cutting - pkg" {
    [configuration] as CFG
    [resiliency] as RES
    [middleware] as MW
    [app channel] as CHAN
    [diagnostics] as DIAG
    [metrics] as MET
    [healthz] as HZ
    [security] as SEC
    [ACL] as ACL
  }

  package "Control-plane libraries - pkg" {
    [actors] as ACT
    [scheduler server] as SCHSRV
    [placement server] as PLSRV
    [sentry CA] as SENTRYCA
    [operator API] as OPERAPI
    [injector patcher] as INJPATCH
  }

  package "Declared and generated surface" {
    [resource types\npkg/apis] as APIS
    [typed clients\npkg/client] as CL
    [proto bindings\npkg/proto] as PB
  }
}

actor "Caller / SDK" as CALLER
actor "Application workload" as APP

cloud "Kubernetes API server" as K8S
database "etcd\nembedded or external" as ETCD
cloud "Component backend\nstate, pubsub, secret,\nconfiguration, binding,\ncrypto, lock" as BACKEND
cloud "Name resolution backend" as NR
cloud "OTLP / Zipkin collector" as OTEL
cloud "Prometheus scraper" as PROM
cloud "Peer sidecar" as PEER
cloud "Control-plane peers" as PEERS
cloud "MCP server backend" as MCPB
cloud "AI / LLM provider" as LLM

CALLER --> APIH
CALLER --> APIG
CALLER --> APIPUB
APP <--> CHAN
PEER --> APIINT
DAPRD --> APIG
DAPRD --> APIH
DAPRD --> UNIV
UNIV --> PROC
PROC --> REG
REG --> SHIM
SHIM --> BACKEND
PROC --> COMPSTORE
PROC --> WF
PROC --> ACT
HOT --> COMPSTORE
SCHCL --> SCHED
ACT --> PLACE
ACT --> SCHED
DAPRD --> CFG
DAPRD --> RES
DAPRD --> MW
DAPRD --> DIAG
DAPRD --> MET
DAPRD --> HZ
APIG --> ACL
APIG --> SEC
DIAG --> OTEL
MET --> PROM
PLUG --> BACKEND
OPER --> OPERAPI
OPER --> K8S
OPERAPI --> APIS
OPERAPI --> CL
INJ --> INJPATCH
INJ --> K8S
SENTRY --> SENTRYCA
SENTRY --> K8S
PLACE --> PLSRV
PLSRV --> PEERS
SCHED --> SCHSRV
SCHSRV --> ETCD
SCHED --> K8S
SCHED --> PLACE
CFG --> APIS
APIS --> PB
DAPRD --> PB
WF --> MCPB
WF --> LLM
PLSRV --> NR
OPERAPI --> K8S

note bottom of DAPRD
  The sidecar alone opens seven listeners:
  the HTTP API port, the public API port,
  the pprof port when profiling is on,
  the gRPC API port, the internal gRPC port,
  the /healthz port and the metrics port.
  pkg/api/grpc/server.go builds two gRPC
  servers from one file with different trust.
end note
note bottom of ETCD
  The scheduler owns this store: it starts an
  embedded etcd unless --etcd-embed=false, in
  which case it is an external cluster named by
  --etcd-client-endpoints.
end note
note bottom of PEERS
  The three control planes reach the Kubernetes
  API server and each other over signed gRPC.
  Placement authority is negotiated at runtime
  from what connected peers advertise, not fixed
  by configuration (AN-018).
end note
@enduml
```

The three tables below hold 42 components, 19 external actors and systems, and 16 entry points. Those are
the row counts of the tables themselves and are stated nowhere else in this inventory.

### Components

| Component | Kind | Responsibility | Implemented by |
|---|---|---|---|
| daprd | Subsystem | The sidecar process: builds its component registries, opens its HTTP, gRPC, internal gRPC, public, pprof, healthz and metrics listeners, and runs the component processor for one application | `cmd/daprd/main.go`; `cmd/daprd/app/app.go` |
| operator | Subsystem | Serves Components, Configurations, Resiliencies, Subscriptions, HTTPEndpoints, MCPServers and WorkflowAccessPolicies to sidecars, and reconciles them from the cluster | `cmd/operator/app/app.go`; `pkg/operator/operator.go` |
| injector | Subsystem | The admission webhook that declines or patches a pod so it carries a sidecar | `cmd/injector/app/app.go`; `pkg/injector/service/handler.go` |
| placement | Subsystem | The actor-placement service: leader election over an actor table and dissemination of it to every connected host | `cmd/placement/app/app.go`; `pkg/placement/placement.go` |
| scheduler | Subsystem | The durable job and actor-reminder service, with its own etcd; also answers actor placement when `--placement-enabled` is set | `cmd/scheduler/app/app.go`; `cmd/scheduler/options/options.go` |
| sentry | Subsystem | Issues, rotates and serves workload certificates and trust bundles; the mTLS trust domain | `cmd/sentry/app/app.go`; `pkg/sentry/sentry.go` |
| dapr-build-tools | Component | The separate `dapr-build-tools` module: the CI helper CLI, not part of the six runtime executables | `.build-tools/main.go`; `.build-tools/cmd/root.go` |
| HTTP API server | Layer | Registers the HTTP routes from the endpoint table and applies max-body, CORS, API-token, tracing, metrics, component-middleware and logging middleware around them | `pkg/api/http/server.go`; `pkg/api/http/endpoints/endpoint.go` |
| gRPC API server | Layer | Builds two gRPC servers from one file with different trust, and serves the `dapr.proto.runtime.v1.Dapr` service | `pkg/api/grpc/server.go`; `pkg/api/grpc/grpc.go`; `pkg/api/grpc/endpoints.go` |
| Internal service-invocation server | Component | The `ServiceInvocation` server sidecars call each other on, separate from the app-facing gRPC API | `pkg/api/grpc/daprinternal.go`; `dapr/proto/internals/v1/service_invocation.proto` |
| universal handler layer | Layer | One implementation per API operation, shared by the HTTP and gRPC surfaces rather than written twice | `pkg/api/universal/` (29 files); `pkg/api/universal/universal.go` |
| Public API listener | Component | A second HTTP listener, opened only when a public port is configured, serving the GET-only public endpoint set over a subset of the primary pipeline (AN-023) | `pkg/api/http/server.go` (§ the `PublicPort` block) |
| gRPC proxy | Component | Relays a gRPC call from the sidecar to the application, with its own director, codec and handler | `pkg/api/grpc/proxy/handler.go`; `pkg/api/grpc/proxy/director.go` |
| compstore | Component | The in-process store of loaded components, subscriptions, configurations and resource metadata | `pkg/runtime/compstore/compstore.go`; `pkg/runtime/compstore/components.go` |
| component processor | Subsystem | Loads, initialises and runs each component category and owns the per-category loops | `pkg/runtime/processor/processor.go`; `pkg/runtime/processor/loops/loops.go` |
| hot reload | Subsystem | Watches the resource sources and reconciles a changed component, configuration, subscription or endpoint into the running sidecar | `pkg/runtime/hotreload/hotreload.go`; `pkg/runtime/hotreload/reconciler/reconciler.go` |
| workflow engine | Subsystem | The durable workflow engine: orchestrators, activities, the actor backend, the in-process backend and the workflow state store | `pkg/runtime/wfengine/wfengine.go`; `pkg/runtime/wfengine/backends/actors/actors.go` |
| scheduler client | Layer | The sidecar's client to the scheduler: connection pool, host watch, leadership and pull slots | `pkg/runtime/scheduler/client/client.go`; `pkg/runtime/scheduler/internal/cluster/cluster.go` |
| pub/sub and subscription pipeline | Component | The CloudEvent adapter, bulk publish and subscribe, the outbox, and the per-protocol subscription postmen | `pkg/runtime/pubsub/adapter.go`; `pkg/runtime/pubsub/outbox.go`; `pkg/runtime/subscription/postman/postman.go` |
| component registries | Subsystem | One registry per component category, with versioning, WASM support and the local loader | `pkg/components/category.go`; `pkg/components/state/registry.go`; `pkg/components/loader/localloader.go` |
| component shims | Layer | One registration file per component implementation; the package builds only under `allcomponents` or `stablecomponents`, and `zz_notag.go` panics otherwise | `cmd/daprd/components/` (156 files); `cmd/daprd/components/zz_notag.go` |
| pluggable component host | Component | Discovers and dials out-of-process components over a unix socket, and wraps them as in-process components | `pkg/components/pluggable/discovery.go`; `pkg/components/pluggable/grpc.go` |
| configuration model | Component | The `Configuration` resource as the runtime reads it, with the validation that refuses a bad spec | `pkg/config/configuration.go`; `pkg/config/acl_trie.go` |
| resiliency provider | Component | Resolves retry, timeout and circuit-breaker policy per app endpoint, per actor type and per component direction | `pkg/resiliency/resiliency.go`; `pkg/resiliency/breaker/circuitbreaker.go` |
| middleware pipeline | Component | The component-middleware contract and the HTTP pipeline that composes it | `pkg/middleware/middleware.go`; `pkg/middleware/http/pipeline.go` |
| app channel | Component | The sidecar's client to the application workload, over HTTP and over gRPC, with its own TLS floor | `pkg/channel/channel.go`; `pkg/channel/http/http_channel.go`; `pkg/channel/grpc/grpc_channel.go` |
| diagnostics | Subsystem | Tracing and monitoring: the OpenTelemetry and Zipkin exporters, the HTTP and gRPC instrumentation, and one monitoring module per building block | `pkg/diagnostics/tracing.go`; `pkg/diagnostics/metrics.go`; `pkg/diagnostics/component_monitoring.go` |
| metrics exporter | Component | The `/metrics` endpoint: a private Prometheus registry gathered together with the default one, plus the caller-supplied extra handlers | `pkg/metrics/exporter.go`; `pkg/metrics/meter.go` |
| healthz | Component | The readiness registry and its `/healthz` listener; `IsReady` is false when no target is registered | `pkg/healthz/healthz.go`; `pkg/healthz/server/server.go` |
| app health | Component | The probe loop against the application's health endpoint, with a failure threshold and a change callback | `pkg/apphealth/health.go`; `pkg/apphealth/status.go` |
| security | Subsystem | SPIFFE identities, mTLS options, workload certificates, and JWT and API-token validation | `pkg/security/security.go`; `pkg/security/spiffe/spiffe.go`; `pkg/security/token.go` |
| ACL | Component | The access-control rules for service invocation and for workflow operations | `pkg/acl/acl.go`; `pkg/acl/workflow/policy.go` |
| actors runtime | Subsystem | The actor table, the placement connector, reminders, timers, reentrancy, and the workflow actor targets | `pkg/actors/actors.go`; `pkg/actors/table/table.go`; `pkg/actors/internal/placement/placement.go` |
| scheduler server | Subsystem | The scheduler's own server: etcd-backed job store, host streams, pull dispatch, concurrency gates and placement mode | `pkg/scheduler/server/server.go`; `pkg/scheduler/server/internal/etcd/etcd.go`; `pkg/scheduler/server/internal/pool/pool.go` |
| placement server | Subsystem | Raft leadership and actor-table dissemination for the placement service | `pkg/placement/placement.go`; `pkg/placement/internal/leadership/leadership.go`; `pkg/placement/internal/server/server.go` |
| sentry CA and validator | Subsystem | The certificate authority and trust bundle, and the JWT and OIDC validators | `pkg/sentry/server/ca/ca.go`; `pkg/sentry/server/validator/validator.go` |
| operator API server | Subsystem | Serves each Dapr resource kind to the sidecars and runs the informer loops that keep it current | `pkg/operator/api/api.go`; `pkg/operator/api/components.go`; `pkg/operator/api/informer/informer.go` |
| injector patcher | Component | Builds the pod patch: the sidecar container, volumes, component annotations and health probes | `pkg/injector/patcher/patcher.go`; `pkg/injector/patcher/sidecar_patcher.go` |
| resource types | Layer | The seven custom resource kinds, with their deep-copy code and the one conversion | `pkg/apis/{components,configuration,resiliency,subscriptions,httpEndpoint,mcpserver,workflowaccesspolicy}/v1alpha1/types.go`; `pkg/apis/subscriptions/v2alpha1/conversion.go` |
| typed clients and informers | Layer | The generated clientset, informers and listers for the resource kinds the operator serves | `pkg/client/clientset/versioned/clientset.go`; `pkg/client/informers/externalversions/factory.go` |
| proto bindings | Layer | The committed generated Go bindings for all nine proto packages, with the hand-written additions committed beside them (AN-017) | `pkg/proto/` (66 files); `dapr/proto/` (37 `.proto`) |
| small contract packages | Component | Four packages that are constants or one derivation: the runtime modes, the CORS defaults, the standalone port chooser and the resource scopes | `pkg/modes/modes.go`; `pkg/cors/cors.go`; `pkg/ports/ports.go`; `pkg/scopes/scopes.go` |

### External actors and systems

| External actor or system | Reached through | Anchor |
|---|---|---|
| Caller — an SDK or a plain HTTP or gRPC client | Remote call | `dapr/proto/runtime/v1/dapr.proto`; `pkg/api/grpc/endpoints.go` |
| Application workload — the process the sidecar serves | Remote call | `pkg/channel/channel.go` (§ the app-channel operations); `pkg/channel/http/http_channel.go`; `pkg/channel/grpc/grpc_channel.go` |
| Peer sidecar — another application's daprd | Remote call | `pkg/api/grpc/daprinternal.go`; `pkg/messaging/direct_messaging.go`; `dapr/proto/internals/v1/service_invocation.proto` |
| Peer control plane — placement, scheduler and sentry as each other's clients | Remote call | `dapr/proto/placement/v1/placement.proto`; `dapr/proto/scheduler/v1/scheduler.proto`; `pkg/security/security.go` |
| Kubernetes API server | Data manager | `pkg/operator/api/api.go`; `pkg/internal/loader/kubernetes/kubernetes.go`; `pkg/injector/service/handler.go` |
| Scheduler etcd — embedded or external cluster | Data manager | `pkg/scheduler/server/internal/etcd/etcd.go`; `cmd/scheduler/options/options.go` (`--etcd-embed`, `--etcd-client-endpoints`) |
| State store component | Data manager | `cmd/daprd/components/state_*.go` (36 registrations); `pkg/components/state/registry.go` |
| Pub/sub broker component | Messaging | `cmd/daprd/components/pubsub_*.go`; `pkg/components/pubsub/registry.go` |
| Secret store component | Service | `cmd/daprd/components/secretstores_*.go`; `pkg/components/secretstores/registry.go` |
| Binding component — input and output | Service | `cmd/daprd/components/bindings_*.go`; `pkg/components/bindings/registry.go` |
| Configuration store component | Service | `cmd/daprd/components/configuration_*.go`; `pkg/components/configuration/registry.go` |
| Crypto provider component | Service | `cmd/daprd/components/crypto_*.go`; `pkg/components/crypto/registry.go` |
| Lock store component | Service | `cmd/daprd/components/lock_redis.go`; `pkg/components/lock/registry.go` |
| Name-resolution backend — Kubernetes DNS, mDNS, Consul, AWS Cloud Map, SQLite | Service | `cmd/daprd/components/nameresolution_*.go`; `pkg/components/nameresolution/registry.go` |
| OTLP or Zipkin collector | Remote call | `pkg/diagnostics/tracing.go`; `go.mod` (`go.opentelemetry.io/otel/exporters/otlp/otlptrace{,grpc,http}`, `.../zipkin`) |
| Prometheus scraper — including the scheduler's `/metrics/scaling` | Service | `pkg/metrics/exporter.go`; `pkg/scheduler/monitoring/scaling/scaling.go` |
| MCP server backend — stdio, SSE or HTTP tool servers | Messaging | `dapr/proto/workflows/v1/mcp.proto`; `pkg/runtime/wfengine/inprocess/mcp/v1/worker.go`; `pkg/runtime/mcpservers.go` |
| AI or model provider — OpenAI, Anthropic, Bedrock, Google AI, Mistral, Ollama, Hugging Face, DeepSeek, echo | Remote call | `cmd/daprd/components/conversation_*.go`; `pkg/components/conversation/registry.go` |
| Application health endpoint — probed for app health | Service | `pkg/apphealth/health.go`; `pkg/channel/http/http_channel.go` |

### Entry points

| ID | Entry point | Kind | Initiated by | Anchor |
|---|---|---|---|---|
| EP-001 | Sidecar HTTP API — one route per operation, versioned by path | Service operation | A caller: an SDK or any HTTP client | `pkg/api/http/server.go` (`setupRoutes`); `pkg/api/http/endpoints/endpoint.go`; `pkg/api/http/http.go` |
| EP-002 | Sidecar gRPC API — the `dapr.proto.runtime.v1.Dapr` service | Service operation | A caller over gRPC | `dapr/proto/runtime/v1/dapr.proto`; `pkg/api/grpc/server.go`; `pkg/api/grpc/endpoints.go` (the allowlist map, AN-012) |
| EP-003 | Public HTTP API — the GET-only endpoint set on the public port | Service operation | A caller on the public listen address and port | `pkg/api/http/server.go` (§ the `PublicPort` block, `s.api.PublicEndpoints()`); `pkg/api/http/endpoints/endpoint.go` |
| EP-004 | Internal service-invocation gRPC API — `ServiceInvocation` | Service operation | A peer sidecar | `dapr/proto/internals/v1/service_invocation.proto`; `pkg/api/grpc/daprinternal.go` |
| EP-005 | Application callback API — `AppCallback`, delivered to the application | Service operation | The sidecar, calling out to the application workload | `dapr/proto/runtime/v1/appcallback.proto`; `pkg/runtime/subscription/postman/http/http.go`; `pkg/channel/grpc/grpc_channel.go` |
| EP-006 | Scheduler gRPC service — job and reminder scheduling, host streams and `ReportActorTypes` | Service operation | Sidecars and peer schedulers | `dapr/proto/scheduler/v1/scheduler.proto` (§ `service Scheduler`); `pkg/scheduler/server/api.go`; `pkg/scheduler/server/server.go` |
| EP-007 | Placement gRPC service — actor-type reporting and actor-table dissemination | Service operation | Sidecars with actors enabled, and peer placement hosts | `dapr/proto/placement/v1/placement.proto`; `pkg/placement/internal/server/server.go`; `pkg/actors/internal/placement/connector/connector.go` |
| EP-008 | Sidecar-injector admission webhook | Service operation | The Kubernetes API server, on pod admission | `pkg/injector/service/handler.go`; `pkg/injector/patcher/patcher.go`; `charts/dapr/charts/dapr_sidecar_injector/templates/dapr_sidecar_injector_webhook_config.yaml` |
| EP-009 | Sentry gRPC service — certificate signing and trust-bundle requests | Service operation | Every other control plane and every sidecar | `dapr/proto/sentry/v1/sentry.proto`; `pkg/sentry/server/server.go` |
| EP-010 | Operator gRPC service — resource lists and subscription streams to sidecars | Service operation | Sidecars asking for Components, Configurations, Resiliencies, Subscriptions, HTTPEndpoints, MCPServers and WorkflowAccessPolicies | `dapr/proto/operator/v1/operator.proto`; `pkg/operator/api/api.go` |
| EP-011 | `/healthz` readiness endpoint | Service operation | The orchestrator's probe, or a caller | `pkg/healthz/server/server.go`; `pkg/healthz/healthz.go` (`IsReady`) |
| EP-012 | `/metrics` Prometheus endpoint | Service operation | A Prometheus scraper | `pkg/metrics/exporter.go`; `pkg/scheduler/monitoring/scaling/scaling.go` |
| EP-013 | pprof profiling endpoint — opened only when profiling is enabled | Service operation | An operator with `--enable-profiling` set | `pkg/api/http/server.go` (§ the `EnableProfiling` block); `cmd/daprd/options/options.go` |
| EP-014 | `dapr-build-tools` CLI | Service operation | A maintainer or a CI workflow | `.build-tools/main.go`; `.build-tools/cmd/root.go`; `.build-tools/cmd/e2e.go` |
| EP-015 | Kubernetes custom resources — the seven Dapr kinds, watched by the operator and, in Kubernetes mode, the sidecar | Platform event | A resource create or update submitted to the cluster | `pkg/apis/` (7 kinds); `pkg/operator/api/informer/informer.go`; `pkg/runtime/hotreload/loader/operator/operator.go` |
| EP-016 | Declarative resources on disk — watched for change and re-read on `SIGHUP` in standalone mode | Platform event | A file being written or replaced | `pkg/runtime/hotreload/loader/disk/disk.go`; `pkg/runtime/hotreload/reconciler/sighup.go`; `pkg/internal/loader/disk/dirdata/dirdata.go` |

## Technology and Size
| Area | Language | Platform and frameworks, with version | Files | Anchor |
|---|---|---|---|---|
| `.build-tools` | Go | A separate module, `dapr-build-tools`; its own `go.mod` and `go.sum`, and a `cmd/` of five drivers — `root`, `e2e`, `perf`, the lint-version check and a tag-guarded variant — with three YAML fixtures under `testdata/` | 14 | `.build-tools/` |
| `.github` | YAML, Python, JavaScript, Shell | GitHub Actions; `dependabot.yml`; 25 workflow definitions under `workflows/`; 82 helper scripts under `scripts/`, 70 of which are version-skew patch files | 120 | `.github/` |
| `charts` | YAML, Go | Helm 3 chart `dapr` with 7 subcharts (`dapr_config`, `dapr_operator`, `dapr_placement`, `dapr_rbac`, `dapr_scheduler`, `dapr_sentry`, `dapr_sidecar_injector`), 7 CRD manifests under `crds/`, and `embed.go` | 67 | `charts/dapr/`; `charts/dapr/Chart.yaml`; `charts/dapr/charts/` (7 subcharts); `charts/dapr/crds/` (7 files) |
| `cmd` | Go | Go 1.26.11; the six executables plus five build and test driver files; `dapr/components-contrib v1.19.1-0.20261126140908-2c9078d426b0`, `dapr/kit v0.19.1-0.20261030175054-be979ab98cc4`, `dapr/durabletask-go v0.16.1-0.20261109153237-98658f38d8cb` | 183 | `cmd/`; `go.mod` (the `go` directive and the first `require` block) |
| `cmd/daprd/components` | Go | One registration file per component family, 156 files; builds only under the `allcomponents` or `stablecomponents` tag, and `zz_notag.go` panics at init otherwise | 156 | `cmd/daprd/components/`; `cmd/daprd/components/zz_notag.go` |
| `CODEOWNERS` | Text | — | 1 | `CODEOWNERS` |
| `CONTRIBUTING.md` | Markdown | — | 1 | `CONTRIBUTING.md` |
| `dapr` | Protocol Buffers (proto3) | 37 `.proto` definitions in 9 packages — `common`, `components`, `internals`, `operator`, `placement`, `runtime`, `scheduler`, `sentry`, `workflows`; seven package READMEs (`scheduler/v1` and `workflows/v1` have none, AN-013) and one directory overview, `dapr/README.md`, which is the eighth non-`.proto` file; `protoc` version disputed across four documents (AN-003) and two more in the committed headers (AN-015); consumed by `connectrpc.com/connect v1.19.1` and `google.golang.org/grpc v1.83.2` | 45 | `dapr/` (45 files: `README.md` + `proto/` 44, of which 37 `.proto` and 7 package READMEs); `go.mod` |
| `docker` | Dockerfile, Shell | Eight Dockerfiles — the runtime image, a debug image, a dev image, Mariner and five Windows base images — plus `docker.mk` and `library-scripts/` of five Debian provisioning scripts | 22 | `docker/`; `docker/Dockerfile`; `docker/docker.mk` |
| `docs` | Markdown, PNG | 198 files: two at the top of the directory, 24 decision records including their index, 13 development files, 153 release-note files, and the six under `docs/artifacts/` this process wrote | 198 | `docs/`; `docs/artifacts/` (6 files, written by this process and not part of the system) |
| `docs/decision_records` | Markdown | 23 records in five categories (`api` 12, `architecture` 3, `cli` 2, `engineering` 4, `sdk` 2) plus `decision_records.md` | 24 | `docs/decision_records/` |
| `docs/development` | Markdown, PNG | Six guides — setup, setup in VS Code, CI, developing, metrics, preview features — plus five screenshots and an image of the debug flow | 13 | `docs/development/` |
| `docs/release_notes` | Markdown, PNG | One note per release from `v0.1.0` to `v1.19.3`, plus `template.md` and an `assets/` folder of 5 PNGs | 153 | `docs/release_notes/` |
| `GOVERNANCE.md` | Markdown | — | 1 | `GOVERNANCE.md` |
| `go.mod` | Go module manifest | `module github.com/dapr/dapr`; `go 1.26.11`; a large direct `require` block, a large indirect block, and four replacement rules plus a licence-motivated replace block | 1 | `go.mod` |
| `go.sum` | Go checksums | The resolved module graph for every `require` in `go.mod` and its transitive closure; states no version of its own | 1 | `go.sum` |
| `grafana` | JSON | Five dashboard definitions — actor, scheduler, sidecar, system-services and workflow — and a README | 6 | `grafana/`; `grafana/grafana-scheduler-dashboard.json` |
| `img` | PNG, SVG, JPEG | Banner, conceptual model and overview diagram, the last embedded by `README.md` | 3 | `img/` |
| `LICENSE` | Text | Apache License 2.0 | 1 | `LICENSE` |
| `pkg/acl` | Go | The service-invocation access-control rules, plus a `workflow/` of 8 files that extracts, holds and evaluates the workflow-operation policy | 10 | `pkg/acl/acl.go`; `pkg/acl/workflow/policy.go` |
| `pkg/actors` | Go | The actor runtime: `api/` 11, `callbackstream/`, `errors/`, `fake/`, `hashing/rendezvous/`, `hostauthz/`, `hostconfig/`, `internal/{apilevel,key,placement,timers}`, `reminders/`, `reserved/`, `router/`, `state/`, `table/`, `targets/{app,workflow}`, `timers/`. The `targets/workflow/` subtree alone holds 124 files | 243 | `pkg/actors/` (243 files); `pkg/actors/actors.go`; `pkg/actors/table/table.go` |
| `pkg/api` | Go | The request path: `net/http` with the `chi`-style router of `pkg/api/http`, gRPC servers on `google.golang.org/grpc v1.83.2`, the internal `ServiceInvocation` server, the 29-file `universal/` handler layer, and the hand-maintained gRPC allowlist map (AN-012) | 124 | `pkg/api/`; `pkg/api/grpc/server.go`; `pkg/api/http/server.go` |
| `pkg/apphealth` | Go | The app-health probe loop: a ticker, a one-slot probe queue, a failure counter with a threshold, and a change callback. Uses `k8s.io/utils/clock` for a testable ticker; imports `pkg/config` for `AppHealthConfig` | 3 | `pkg/apphealth/health.go`; `pkg/apphealth/status.go` |
| `pkg/apis` | Go | The seven custom resource kinds — `components`, `configuration`, `httpEndpoint`, `mcpserver`, `resiliency`, `subscriptions` (two served versions with a conversion), `workflowaccesspolicy` — each with a `v1alpha1/types.go` and deep-copy code, over `common/` and the three helpers `namevalue.go`, `scoped.go`, `tls.go` | 46 | `pkg/apis/` (46 files); the tree listing of `pkg/apis/` |
| `pkg/backoff` | Go | A jitter helper and its test | 2 | `pkg/backoff/jitter.go` |
| `pkg/buildinfo` | Go | Build-time version and enabled-feature values read through `-ldflags` | 2 | `pkg/buildinfo/buildinfo.go` |
| `pkg/channel` | Go | The app channel: an HTTP client for the application workload, a gRPC client for the same, `crypto/tls` with `AppChannelMinTLSVersion = tls.VersionTLS12`, and the fake and testing doubles | 14 | `pkg/channel/channel.go`; `pkg/channel/http/http_channel.go`; `pkg/channel/grpc/grpc_channel.go` |
| `pkg/client` | Go | The generated Kubernetes clientset, informers and listers for the resource kinds the operator serves — 28 files of clientset, 9 of informers, 4 of listers | 41 | `pkg/client/`; `pkg/client/clientset/versioned/clientset.go` |
| `pkg/components` | Go | The component registries: 15 category constants, one registry per category, the pluggable-component discovery and gRPC clients, and the state and lock configuration decoders. Against `dapr/components-contrib v1.19.1-0.20261126140908-2c9078d426b0` | 53 | `pkg/components/category.go`; `pkg/components/{state,pubsub,bindings,secretstores,configuration,lock,nameresolution,middleware,search,vector,binarystore,conversation,crypto}/registry.go` |
| `pkg/config` | Go | The Configuration resource as the runtime reads it: the seven preview features and their defaults, the actor-state binding rules, the workflow and activity limits, the preview ACL and secrets scopes, the metric, tracing, mTLS and pipeline specs. `gopkg.in/yaml.v3`, `k8s.io/apimachinery`, `github.com/spf13/cast` | 30 | `pkg/config/configuration.go`; `pkg/config/testdata/` (19 fixtures) |
| `pkg/cors` | Go | Two constants and nothing else: the empty default origin and the `*` wildcard | 1 | `pkg/cors/cors.go` |
| `pkg/diagnostics` | Go | Tracing and monitoring: OpenTelemetry and Zipkin exporters (`go.opentelemetry.io/otel/exporters/otlp/otlptrace{,grpc,http}`, `.../zipkin`), the `ocgrpc` and OpenCensus views behind every `dapr_*` metric name, and one monitoring module per building block | 35 | `pkg/diagnostics/tracing.go`; `docs/development/dapr-metrics.md` (which names each module) |
| `pkg/encryption` | Go | State-store envelope encryption: the key and cipher handling, and the state wrapper | 4 | `pkg/encryption/encryption.go`; `pkg/encryption/state.go` |
| `pkg/expr` | Go | One expression helper and its test | 2 | `pkg/expr/expr.go` |
| `pkg/healthz` | Go | The readiness mechanism: a target registry, a per-target healthy/unhealthy flag, a lock, and `target.go`. `IsReady` is false when no target is registered | 8 | `pkg/healthz/healthz.go`; `pkg/healthz/target.go`; `pkg/healthz/server/server.go` |
| `pkg/healthz/server` | Go | The `/healthz` HTTP listener, one per process, honouring `--healthz-listen-address` | 2 | `pkg/healthz/server/server.go` |
| `pkg/injector` | Go | The admission webhook: `service/` 12 files (the handler, the config, the metrics and the service-account matcher), `patcher/` 14 files (container, volumes, components, sidecar), `annotations/` and `consts/` | 29 | `pkg/injector/service/handler.go`; `pkg/injector/patcher/patcher.go` |
| `pkg/internal` | Go | The resource loaders: `disk/` 16 files over `dirdata/` (5), `kubernetes/` 9, and `validate/` 5 for MCP servers and workflow access policies | 32 | `pkg/internal/loader/loader.go`; `pkg/internal/loader/disk/disk.go`; `pkg/internal/loader/kubernetes/kubernetes.go` |
| `pkg/messages` | Go | The legacy API error set in `predefined.go`, the `api_error.go` type, and `errorcodes/` | 5 | `pkg/messages/predefined.go`; `pkg/messages/api_error.go` |
| `pkg/messaging` | Go | The service-invocation message types and the direct-messaging client: `InvokeMethodRequest`/`InvokeMethodResponse`, the replayable request body, the method-name normaliser, and the gRPC proxy | 16 | `pkg/messaging/v1/messaging.go`; `pkg/messaging/direct_messaging.go`; `pkg/messaging/v1/` (9 files) |
| `pkg/metrics` | Go | The Prometheus exporter: `contrib.go.opencensus.io/exporter/prometheus` on a private `prometheus/client_golang` registry, gathered together with the default registry so third-party collectors are served, plus the caller-supplied extra handlers | 6 | `pkg/metrics/exporter.go`; `pkg/metrics/{meter,options}.go` |
| `pkg/middleware` | Go | The component-middleware contract: a single `HTTP func(http.Handler) http.Handler` type, the `http/` pipeline that composes it, and the `store/` that holds the loaded handlers | 7 | `pkg/middleware/middleware.go`; `pkg/middleware/http/pipeline.go` |
| `pkg/modes` | Go | Two constants and nothing else: the `kubernetes` and `standalone` runtime modes | 1 | `pkg/modes/modes.go` |
| `pkg/operator` | Go | The operator: `api/` 21 files serving each resource kind, `informer/` 6, `loops/` 4, `cache/` 4 for pod metadata, plus `handlers/`, `meta/`, `monitoring/`, `watchdog.go` | 38 | `pkg/operator/operator.go`; `pkg/operator/api/api.go` |
| `pkg/outbox` | Go | The outbox that makes a publish idempotent across a state transaction, with its fake | 3 | `pkg/outbox/outbox.go` |
| `pkg/placement` | Go | The placement service: `internal/` 21 files over `authorizer/`, `leadership/` (with `fsm/`, `logr/`, `spiffenet/`), `loops/` 14 and `server/`, plus `hashing/`, `monitoring/` and `peers/` | 26 | `pkg/placement/placement.go`; `pkg/placement/internal/leadership/leadership.go` |
| `pkg/ports` | Go | The standalone port chooser: a SHA-256 over the app ID, working directory, user ID and hostname, mapped into the port range above the requested start, checked for availability and otherwise left to `phayes/freeport` | 2 | `pkg/ports/ports.go` |
| `pkg/proto` | Go, generated | `protoc-gen-go`, `protoc-gen-go-grpc` and `protoc-gen-connect-go` output, committed rather than built at release time; 66 files including 9 Connect handlers | 66 | `pkg/proto/` |
| `pkg/resiliency` | Go | The policy provider: retry, timeout and circuit breaker resolved per app endpoint, per actor type before and after the lock, and per component direction, over `dapr/kit/retry` and an `lru.Cache` of breaker instances per app and per actor type | 13 | `pkg/resiliency/resiliency.go`; `pkg/resiliency/breaker/` (2 files) |
| `pkg/responsewriter` | Go | A fork of the negroni response writer, carrying its upstream licence | 3 | `pkg/responsewriter/response_writer.go`; `pkg/responsewriter/README.md` |
| `pkg/retry` | Go | One retry helper and its test | 1 | `pkg/retry/retry.go` |
| `pkg/runtime` | Go | The runtime core, 308 files: compstore 25, processor 61, hot reload 44, pub/sub 30, subscription 23, scheduler client 26, wfengine 62, with `authorizer/`, `channels/`, `errors/`, `meta/`, `registry/` and the top-level `runtime.go`, `actorstatestores.go` and `mcpservers.go` | 308 | `pkg/runtime/`; `pkg/runtime/runtime.go`; `pkg/runtime/actorstatestores.go` |
| `pkg/scheduler` | Go | The scheduler process: `server/internal/` 68 files over `etcd/`, `pool/` 33 with its pull dispatch, `placement/` 12, `controller/`, `cron/`, `handoff/`, `authz/`, `serialize/`, plus `monitoring/scaling/` and `client/` | 76 | `pkg/scheduler/server/server.go`; `pkg/scheduler/server/internal/etcd/etcd.go` |
| `pkg/scopes` | Go | The resource-scope matcher an operator resource is filtered by | 2 | `pkg/scopes/scopes.go` |
| `pkg/security` | Go | SPIFFE identities, workload certificates, mTLS options, the API token and JWT handling, with `consts/`, `fake/`, `spiffe/` and `token/` | 13 | `pkg/security/security.go`; `pkg/security/spiffe/spiffe.go` |
| `pkg/sentry` | Go | Sentry: `server/ca/` 15 files (the bundle, the JWT, the Kubernetes and self-hosted sources, validation), `server/validator/` 10 (insecure, JWKS, Kubernetes), `server/oidc/`, `server/images/`, plus `config/` and `monitoring/` | 36 | `pkg/sentry/sentry.go`; `pkg/sentry/server/ca/ca.go` |
| `pkg/sse` | Go | One server-sent-events helper | 1 | `pkg/sse/sse.go` |
| `pkg/testing` | Go | The 26-file mock substrate: app channel, app, bindings, component, context, direct messaging, failure, state store, lock, name resolution, pubsub adapter, querier, resiliency, secrets, subtle crypto and transactional store mocks, plus a gRPC server, a trace helper and a null logger | 26 | `pkg/testing/`; `pkg/testing/component.go` |
| `pkg/validation` | Go | One validation helper and its test | 2 | `pkg/validation/validation.go` |
| `README.md` | Markdown | — | 1 | `README.md` |
| `RELEASE.md` | Markdown | — | 1 | `RELEASE.md` |
| `SECURITY.md` | Markdown | — | 1 | `SECURITY.md` |
| `swagger` | JSON | The generated OpenAPI description of the HTTP API | 1 | `swagger/swagger.json` |
| `tests` | Go, YAML, C#, Java, Python, PHP, JavaScript, Shell, Bicep | The test substrate, 3 335 files: `integration/suite/` 2 073 over the nine areas and a 185-file framework, `apps/` 267 of sample applications in eight languages, `perf/` 631 including 580 committed chart images, `e2e/` 47, `config/` 76 manifests, and `test-infra/` 12 Bicep and shell files | 3 335 | `tests/`; `tests/README.md`; `tests/integration/suite/` (2 073 files) |
| `tools` | Go, Shell | The proto generation script, the licence boilerplate template, `codegen.mk` and a `tools.go` that pins the generator dependencies | 4 | `tools/proto/generate.sh`; `tools/boilerplate.go.txt`; `tools/codegen.mk` |
| `utils` | Go | The cross-package helpers: host resolution, HTTP middlewares, the Kubernetes client helper, `resolvconf.go` for DNS configuration | 9 | `utils/utils.go`; `utils/host.go`; `utils/resolvconf.go` |

## Persistent Data
Which stores this system keeps, and which it only reaches. The distinction the code draws: a store whose
lifecycle DAPR Core owns is listed; a store it merely writes through a configured component is listed at the
boundary, with the component named as the data manager.

```plantuml
@startuml
title Persistent state - owned by the system, or reached through it
skinparam componentStyle rectangle
skinparam wrapWidth 250

package "DAPR Core owns" {
  [Scheduler etcd\ndata dir, WAL, snapshots] as S1
  [In-memory component state\nstate_in-memory, pubsub_in-memory,\nsearch_in-memory, vector_in-memory] as S2
  [Local file stores\nsecretstores_local_file,\nbindings_localstorage,\ncrypto_localstorage] as S3
  [Actor timers held in process\npkg/actors/internal/timers] as S4
}

package "Declared in the repository, stored in the cluster" {
  [Component] as C1
  [Configuration] as C2
  [Resiliency] as C3
  [Subscription] as C4
  [HTTPEndpoint] as C5
  [MCPServer] as C6
  [WorkflowAccessPolicy] as C7
}

package "Reached through a configured component" {
  [Actor and workflow state\nvia the actor state store component] as A1
  [Scheduler job and reminder\nrecords inside etcd] as A2
  [SQLite state store\nmodernc.org/sqlite in-process] as A3
}

[Kubernetes API server\nthe store for the seven CRDs] as K8S
[Embedded etcd server] as ETCD
[Configured external store] as EXT

C1 --> K8S
C2 --> K8S
C3 --> K8S
C4 --> K8S
C5 --> K8S
C6 --> K8S
C7 --> K8S
S1 --> ETCD
A2 --> ETCD
A1 --> EXT
A3 --> EXT

note bottom of A1
  The runtime does not choose the actor state store and
  does not require one at start-up. pkg/runtime/actorstatestores.go
  declares one function, actorStateStoreBindings(spec, namespace,
  appID) map[string]string: it returns the store each actor type
  is bound to by spec.actors.stateStores, and adds the app's
  workflow actor types when spec.workflow.stateStore is set. It
  returns no error and cannot fail the runtime.
  What is enforced is the binding rule, at configuration load:
  Configuration.validate() calls ActorsSpec.Validate(), which
  rejects a binding that names no store, lists no actor type,
  repeats a type, binds a reserved dapr.internal type, or binds
  one type to two stores. Validate is nil-safe, so a
  Configuration with no spec.actors raises none of these.
end note
note bottom of C4
  Subscription exists in two versions, v1alpha1 and v2alpha1,
  with a conversion between them. It is the only CRD carrying
  two served versions.
end note
@enduml
```

| Store | Kind | Data manager | Anchor |
|---|---|---|---|
| Scheduler etcd — the durable job and actor-reminder store | Structured document | Embedded etcd server started by the scheduler, `go.etcd.io/etcd/server/v3 v3.5.33`; or an external etcd cluster when `--etcd-embed=false` | `cmd/scheduler/options/options.go` (`--etcd-embed`, `--etcd-data-dir`, `--etcd-initial-cluster`, `--etcd-client-endpoints`); `pkg/scheduler/server/internal/etcd/etcd.go`; `go.mod` |
| The seven Dapr custom resources — `Component`, `Configuration`, `Resiliency`, `Subscription`, `HTTPEndpoint`, `MCPServer`, `WorkflowAccessPolicy` | Structured document | Kubernetes API server, through `k8s.io/client-go v0.32.3` informers; `Subscription` is served as both `v1alpha1` and `v2alpha1` with a conversion | `apis/{components,configuration,resiliency,subscriptions,httpEndpoint,mcpserver,workflowaccesspolicy}/v1alpha1/types.go`; `apis/subscriptions/v2alpha1/conversion.go`; `charts/dapr/crds/` (7 files) |
| Actor and workflow state | Relational schema where the configured actor state store component is a SQL store; the store itself is outside the boundary | A state-store component, bound per actor type. `actorStateStoreBindings` reports those bindings and enforces nothing; the binding rules are refused at configuration load by `ActorsSpec.Validate`. Which component serves an unbound actor type is decided by the actor state resolver, which this inventory has not opened | `pkg/runtime/actorstatestores.go` (`actorStateStoreBindings`); `pkg/config/configuration.go` (`ActorsSpec.Validate`, `StateStoreByActorType`, `WorkflowSpec.GetStateStore`); `pkg/actors/state/resolver.go` (unread); `pkg/runtime/wfengine/state/` (9 files); `pkg/config/testdata/{actor_state_stores_config.yaml,invalid_actor_state_stores_config.yaml,workflow_state_store_config.yaml}` |
| SQLite state store | Relational schema | `modernc.org/sqlite v1.42.2` through the state-store shim, in-process | `cmd/daprd/components/state_sqlite.go`; `go.mod` |
| Local file secret store | Record file | Files read from a configured directory | `cmd/daprd/components/secretstores_local_file.go` |
| Local file crypto store | Record file | Keys read from a configured directory | `cmd/daprd/components/crypto_localstorage.go` |
| Local storage binding | Record file | Files written to a configured directory | `cmd/daprd/components/bindings_localstorage.go` |
| In-memory component stores — state, pub/sub, search and vector | Structured document | Process memory; lost when the sidecar exits | `cmd/daprd/components/state_in-memory.go`, `pubsub_in-memory.go`, `search_in-memory.go`, `vector_in-memory.go` |
| Actor timers held by the host | Structured document | In-process timer table, not durable; actor reminders are durable through the scheduler instead | `pkg/actors/internal/timers/inmemory/` (4 files); `pkg/actors/timers/timers.go` |

## Documentation
What the delivery says about itself, graded against the tree. The owner's `.iari/README.md` names the stakeholder
and points to no document, so the perimeter documents were found from the tree and from each other.

```plantuml
@startuml
title Documentation the delivery carries, graded against the repository tree
skinparam componentStyle rectangle
skinparam wrapWidth 250

package "Current - nothing in the tree contradicts it" {
  [README.md\nsystem statement] as D1
  [docs/README.md\nin-tree docs index] as D2
  [docs/CONTRIBUTING.md\nproject contribution contract] as D3
  [CONTRIBUTING.md\nupstream contribution guide] as D4
  [GOVERNANCE.md\nmaintainer governance] as D5
  [CODEOWNERS\nreview ownership] as D6
  [LICENSE\nApache-2.0] as D7
  [RELEASE.md and SECURITY.md\none-line pointers out of the tree] as D8
  [docs/development/\nsetup, build, debug, metrics] as D9
  [docs/release_notes/\n147 notes, v0.1.0 to v1.19.3] as D10
  [docs/release_notes/template.md] as D11
  [.github/pull_request_template.md] as D12
  [.github/ISSUE_TEMPLATE/\n9 forms] as D13
  [pkg/api/errors/README.md\nthe rich error model] as D14
  [pkg/responsewriter/README.md\nnegroni fork and its provenance] as D15
  [tests/README.md\nthe nine directories of the substrate] as D16
  [.build-tools/README.md\nthe build-tools CLI] as D17
}

package "Contradicted by the code" {
  [docs/decision_records/decision_records.md\nindexes ARC-004, absent from the tree] as X1
  [dapr/README.md\npackage table omits scheduler/ and workflows/] as X2
  [tools/proto/generate.sh\nreads proto inputs the tree does not hold] as X3
  [dapr/proto/runtime/v1/README.md\nprotoc v4.25.4] as X4
  [.github/copilot-instructions.md\nprotoc v34.1] as X5
  [pkg/runtime/wfengine/README.md\nstates no purge and no workflow e2e test] as X6
  [charts/dapr/README.md\nchart details name 6 of the 7 subcharts] as X7
}

package "Outdated - true when written, not true now" {
  [docs/decision_records/**\nno record added since 2023-09-11] as O1
  [docs/development/\nsetup-ci.md predates the current CI] as O2
  [cmd/*/README.md\nfive three-line redirects to docs.dapr.io] as O3
  [docs/development/README.md\nindexes 5 of the 6 guides] as O4
}

note bottom of X6
  The README states that completed workflow state is
  never purged and must be cleaned by hand, and that
  no end-to-end test targets workflows. The tree holds
  pkg/actors/targets/workflow/retentioner/, the
  stateRetentionPolicy fields of the Configuration
  spec, tests/integration/suite/daprd/workflow/ (836
  files) and tests/e2e/workflows/ (AN-021).
end note
note bottom of X7
  The chart details introduce the CRDs, the operator,
  the sidecar injector, Sentry, Placement and the
  Scheduler - six items. charts/dapr/charts/ holds seven
  subcharts; the seventh, dapr_config, renders the
  default Configuration resource every sidecar reads
  (AN-022).
end note
note bottom of O2
  setup-ci.md describes a CI of build, docker push and
  GitHub release on PR, push and tag. .github/workflows/dapr.yml
  still does those, but now also runs a merge-queue gate,
  five workflow-mode integration jobs, an in-flight
  workflow upgrade job, a scheduler-placement leg, CodeQL,
  govulncheck, a gen-proto diff check and a helm release
  chain. The guide names none of them.
end note
note bottom of O4
  docs/development/ holds six guides. The index links
  five of them; preview-features.md, the only in-tree
  statement of how a feature flag is declared, toggled
  and removed at GA, is not among them (AN-022).
end note
note bottom of D16
  It names the nine directories the substrate holds and
  each exists. It states no behaviour of the system
  under test, so nothing in it can go stale against the
  code it exercises.
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
| `docs/decision_records/decision_records.md` | The decision-record index, the five category prefixes, the naming rule and the required fields of a record | Contradicted by the code — it links `ARC-004-http-server.md`, and `docs/decision_records/architecture/` holds three records; the index counts 24 links against 23 files | `docs/decision_records/decision_records.md`, § Dapr decision record organization and index (Architecture list, fourth entry) against `docs/decision_records/{api,architecture,cli,engineering,sdk}/` (23 records) |
| `docs/decision_records/**` — 23 records in ARC, API, CLI, SDK, ENG | The architecture, API, CLI, SDK and engineering decisions in force | Outdated — the newest record was committed on 2023-09-11 (`c338385`), while features landed through 2026-11 (scheduler placement authority `3d9dbe6`, search and vector blocks `04215fd`, workflow schedules `61914fd`); the only later commit touching the folder is a typo fix (`62d5128`, 2026-11-25) | `docs/decision_records/{architecture,api,cli,sdk,engineering}/`; commit log of the folder |
| `docs/development/README.md` | The index of the development guides | Outdated — it lists five of the six guides and omits `preview-features.md`, the only in-tree statement of how a feature flag is declared, toggled and removed at GA (AN-022) | `docs/development/README.md` (5 links) against `docs/development/` (six guides) |
| `docs/development/developing-dapr.md`, `dapr-metrics.md`, `preview-features.md` | How to build, test and lint the runtime; the metric catalogue per process and per component with the source file of every group; how a preview feature is declared, toggled and removed at GA | Current — the commands `developing-dapr.md` gives (`make build`, `make test`, `make check`, `make docker-deploy-k8s`, golangci-lint v2.10.1) match `.github/copilot-instructions.md` and the `Makefile` targets; every source file `dapr-metrics.md` names exists under `pkg/{operator,injector,placement,sentry,scheduler}/monitoring/` or `pkg/diagnostics/`, and it marks the metrics retired in 1.17 as retired; the seven feature constants and `IsFeatureEnabled` that `preview-features.md` describes are the ones `pkg/config/configuration.go` declares | `docs/development/developing-dapr.md`, § Build the Dapr binaries / Run unit tests; `docs/development/dapr-metrics.md`, § Dapr Common metrics through § Job metrics; `docs/development/preview-features.md`, § Declaring / Toggling / Using / Release GA; `pkg/config/configuration.go` (the `Feature` constants and `IsFeatureEnabled`) |
| `docs/development/setup-ci.md` | How to set up GitHub Actions in a fork: the secrets it needs, and what a pull request, a push to the default branch and a release tag each trigger | Outdated — its trigger table still matches `.github/workflows/dapr.yml` for the pull-request, push and tag cases, but it describes a CI of build, docker push and GitHub release and names none of the gates the workflow now runs: the merge-queue `required` job, the five workflow-mode integration jobs, the in-flight workflow upgrade job, the scheduler-placement leg, CodeQL, `govulncheck`, the gen-proto diff check and the helm release chain | `docs/development/setup-ci.md`, § Trigger the build against `.github/workflows/dapr.yml` (the `required`, `integration-tests-workflow-modes`, `integration-tests-workflow-upgrade`, `integration-tests-scheduler-placement`, `helm` and `helmpublish` jobs) |
| `docs/development/setup-dapr-development-env.md`, `setup-dapr-development-using-vscode.md` | Environment setup, on the host and in a dev container | Not read — the two guides this inventory has not opened, so nothing is claimed about them | `docs/development/` |
| `docs/release_notes/` — 147 notes, `v0.1.0` to `v1.19.3`, plus a template and 5 asset images | What changed in each release: the problem, the impact, the root cause and the solution, per fix | Current — the newest notes (`v1.19.3`, `v1.18.12`, `v1.17.21`) accompany commits of 2026-11-26 | `docs/release_notes/` (153 files: 147 notes, `template.md`, `assets/` 5 files); `docs/release_notes/v1.19.3.md` |
| `docs/release_notes/template.md` | The shape of a release note and its substitution variables | Current — its sections are the ones the newest notes fill | `docs/release_notes/template.md`, § New in this release / Upgrading / Breaking Changes / Deprecation Notices |
| `.github/copilot-instructions.md` | Build, test, lint, formatting and convention guidance for a contributor or an agent: package layout, build tags, the licence header, import grouping, test patterns, the PR checklist | Contradicted by the code — the build, test and lint guidance matches the tree, but it states `protoc` v34.1 while `dapr/proto/runtime/v1/README.md` states v4.25.4, `dapr/README.md` states v25.4 and `tools/proto/generate.sh` pins 3.10.0 | `.github/copilot-instructions.md`, § Protobuf / gRPC (required tools) |
| `.github/pull_request_template.md` | The pull-request checklist a change is asked to satisfy | Current — every item is checkable against the tree (compile, tests, docs and spec issue in `dapr/docs`, sample) | `.github/pull_request_template.md`, § Checklist |
| `.github/ISSUE_TEMPLATE/` — 9 forms | The intake shape of a bug, a discussion, an endgame, a feature request, a minor patch release, a proposal, a question and a release planning item | Current | `.github/ISSUE_TEMPLATE/` |
| `cmd/daprd/README.md`, `cmd/injector/README.md`, `cmd/operator/README.md`, `cmd/placement/README.md`, `cmd/sentry/README.md` | A pointer to the published documentation of each executable | Outdated as documentation — each is a three-line redirect to `docs.dapr.io`, so no in-tree document states how any of the five behaves; `cmd/scheduler/` carries no README at all (AN-011) | `cmd/daprd/README.md`; `cmd/injector/README.md`; `cmd/operator/README.md`; `cmd/placement/README.md`; `cmd/sentry/README.md` |
| `dapr/README.md` | The proto package overview and the client-generation steps | Contradicted by the code — the package table lists `common`, `internals`, `runtime`, `operator`, `placement`, `sentry`, `components`, and the tree also holds `scheduler/` and `workflows/`; it names `protoc` v25.4 | `dapr/README.md`, § Overview and § Proto client generation |
| `dapr/proto/**/README.md` — 7 package notes | Per-package proto notes and the generation prerequisite | Contradicted by the code for `runtime/v1` (protoc v4.25.4); the other six state no version | `dapr/proto/runtime/v1/README.md`, § Proto client generation |
| `pkg/runtime/wfengine/README.md` | How the workflow engine works: internal actors, the workflow state key scheme, the reminder-per-step execution model, how to build and test it | Contradicted by the code — it states "there is no automatic purging of state for completed workflows" and "There are no end-to-end tests that directly target the Dapr Workflows engine yet"; the tree holds `pkg/actors/targets/workflow/retentioner/`, the `stateRetentionPolicy` fields of the workflow spec in `pkg/config/configuration.go`, `tests/integration/suite/daprd/workflow/` (836 files) and `tests/e2e/workflows/` (AN-021) | `pkg/runtime/wfengine/README.md`, § State storage ("IMPORTANT") and § Manual testing; `pkg/actors/targets/workflow/retentioner/` (2 files: `factory.go`, `retentioner.go`); `pkg/config/configuration.go` (`WorkflowStateRetentionPolicy`); `tests/integration/suite/daprd/workflow/` (836 files) |
| `pkg/api/errors/README.md` | How to add an error to the rich error model: check for an existing file, name it after the building block, build it with the `github.com/dapr/kit/errors` builder and `WithFieldViolation`, and test it where the state API's errors are tested | Current — both files it names as the location of errors exist (`pkg/messages/predefined.go` for the legacy set, `pkg/api/errors/` for the new one), its named reference implementation `pkg/api/errors/state.go` exists, and the two integration files it points at are under `tests/integration/suite/daprd/state/` | `pkg/api/errors/README.md`, § Step 1 Understanding the Current Error Handling and § Step 3 Designing Rich Error Messages; `pkg/api/errors/` (7 files, `state.go` among them); `tests/integration/suite/daprd/state/{grpc,http}/` |
| `pkg/responsewriter/README.md` | The provenance of a forked package: which upstream it came from, at which commit, and the MIT licence it carries | Current — `response_writer.go` sits beside it and the licence text is the upstream's | `pkg/responsewriter/README.md`, § License; `pkg/responsewriter/response_writer.go` |
| `tests/README.md` | The layout of the test substrate: what each of the nine directories holds, and a pointer to the how-to guides under `tests/docs/` | Current — all nine directories it names exist, and the five guides it links are among the eight files under `tests/docs/` | `tests/README.md`, § End-to-end tests; `tests/docs/` (8 files) |
| `.build-tools/README.md` | What the separate `dapr-build-tools` module is, how to run it from source and from a compiled binary | Current — the directory holds its own `main.go`, `go.mod` and a `cmd/` of subcommands, which is what the note describes | `.build-tools/README.md`, § Running the CLI; `.build-tools/` (14 files: `main.go`, `go.mod`, `cmd/` 6, `testdata/` 3) |
| `charts/dapr/README.md` | The deployment reference: every chart parameter with its default — registry, tag, HA, mTLS, per-service replicas, resources, the scheduler's etcd tuning, the injector's service-account allowlist | Contradicted by the code in one place — the option tables are current and match the chart's `values.yaml`, but § Chart Details names six of the seven subcharts, omitting `dapr_config`, the one that renders the Configuration resource every sidecar reads (AN-022). `charts/dapr/charts/` holds seven | `charts/dapr/README.md`, § Chart Details and § Configuration (the global, operator, placement, RBAC, scheduler, sentry and sidecar-injector option tables) against `charts/dapr/charts/dapr_config/templates/dapr_default_config.yaml` |
| `tools/proto/generate.sh` | How proto clients for the SDKs are generated, and which proto files are the inputs | Contradicted by the code — the `generate()` loop reads `${top_root}/pkg/proto/dapr/dapr.proto` and `${top_root}/pkg/proto/daprclient/daprclient.proto`; `pkg/proto/` holds 66 generated `.pb.go` files and no `.proto`, and the definitions live under `dapr/proto/`. It also pins protoc 3.10.0 and the Java gRPC plugin 1.24.1 | `tools/proto/generate.sh`, § generate() and § Download and install tools |
| `.iari/README.md` | Who leads the project | Current — stakeholder input, not part of the delivery | `.iari/README.md` |

## History
Each row states the commits listed and the period they cover. A reading is drawn only from what those commits
show being added or taken out — never from how the code looks today.

| Area | Commits read and period | What was removed or reworked | Reading | Commits |
|---|---|---|---|---|
| `cmd` | 25 commits, 2026-06-23 → 2026-11-17 | The placement-to-scheduler cutover ran as five numbered steps (add protos, add scheduler placement, helm and flags, daprd takes placement from the scheduler, authority detection with rollback); search and vector building blocks added; binary-store building block added; metrics listen address made configurable on sentry, operator and injector; scheduler made to exit when it cannot bind its port and to retry client creation instead of exiting | Unstable | `2707e0a`, `effad08`, `9000808`, `1275498`, `04215fd`, `ee35baa`, `d221275`, `180905c`, `9abc62a`, `397d859`, `ec2ac5f`, `8608664`, `ece6ec7`, `3d9dbe6`, `9aeab3b`, `0ba3c1b`, `e87872a`, `5e54d0c`, `f2d92bc`, `5aeedcb`, `b8216ba`, `35a729a`, `6365688`, `717d62f`, `b334e5f` |
| `dapr` (proto contracts) | 25 commits, 2026-04-20 → 2026-11-18 | Scheduler placement protos added; actor list/get timers added; the MCP proto types replaced with the upstream Go SDK's types; workflow access policy extended to all operations; global and per-name concurrency limits added; cross-app workflow operations added; pull dispatch of reminders to sidecars added | Unstable | `14c3158`, `f664c59`, `5573c4e`, `2dab9fd`, `effad08`, `04215fd`, `397d859`, `3759a59`, `1d282c9`, `92bbb0e`, `3d9dbe6`, `e2030a5`, `19e9dbd`, `cd72c15`, `8fd31d1`, `df9137c`, `a86c03b`, `d16e4de`, `6d8290e`, `88a04dd`, `1ebb858`, `1b4bef8`, `39c1ef7`, `c581fa8`, `c8463fc` |
| `.github` | 25 commits, 2026-10-13 → 2026-11-26 | CI reworked for duration and reliability rather than for new function: a dedicated integration-test build cache and more parallelism, more packages per KinD run, a required gate because of generated names, DCO fixed for the merge queue, backport auto-merge added, version-skew runs retargeted as the previous minor moved, MacOS runner versions bumped, the perf cluster moved to a new region | Unstable | `acde1d4`, `14eea71`, `c783324`, `79b189f`, `9d1b21a`, `75a49d9`, `41ac782`, `a872136`, `9000808`, `e92225e`, `a8e26cf`, `edff789`, `c5a9d80`, `903a585`, `24f5c3c`, `cbeb475`, `a24a198`, `7f1b470`, `a4b1184`, `66f70d0`, `04215fd`, `aed36f3`, `c2c0842`, `0d489b3`, `10d05f1` |
| `docs/decision_records` | 20 commits, 2020-04-16 → 2026-11-25 | Records added until 2023-09-11 and nothing after it; a mis-titled file corrected in 2023-08; one record renumbered from API-008 to API-009 in 2020 when the numbering collided; the only commit after 2023 is a typo fix in 2026-11 | Consolidating — dormant rather than settling | `62d5128`, `c338385`, `0904549`, `bac6ae6`, `855f09c`, `3ab2591`, `6299e15`, `f4cc6b8`, `47b8651`, `856d8cd`, `8f34ff0`, `71086c4`, `08b630d`, `9f4356c`, `39335d8`, `5f6e428`, `d325087`, `5295e0a`, `50988ac`, `904a928` |
| `docs/release_notes` | 20 commits, 2026-10-30 → 2026-11-26 | A run of security and correctness fixes each took its own patch note across the 1.17, 1.18 and 1.19 lines: SQLite driver update, gRPC header cap and constant-time token compare, forged outbox messages, malformed encrypted state, ACL verb rules without metadata, brotli advisory, Redis pub/sub read timeout | Stable — one note per fix, no rework of older notes | `ffbf163`, `0337c32`, `298d459`, `cd15eb6`, `b7d4231`, `4313e79`, `12c6cbd`, `29f2d37`, `4433e2f`, `7ee17e2`, `f09a418`, `0be3f04`, `55312d1`, `c10b835`, `bd54d83`, `3342040`, `f2baba4`, `ee602ae`, `6eb861d`, `30eeca1` |
| `go.mod` | 20 commits, 2026-10-13 → 2026-11-26 | The Go toolchain moved three times (1.26.9 → 1.26.10 → 1.26.11); `components-contrib`, `kit` and `durabletask-go` were pinned to exact pseudo-versions after periods of tracking HEAD; OpenTelemetry, gorilla/websocket and lz4 bumped together for `govulncheck`; `modernc.org/sqlite` bumped to stop leaking read transactions; brotli bumped for an advisory | Unstable | `ffbf163`, `f1895d6`, `298d459`, `4313e79`, `61914fd`, `24bfe82`, `2dab9fd`, `dc63861`, `effad08`, `c10b835`, `1275498`, `cded45a`, `9dcb298`, `d23b217`, `02f0b3c`, `accd8c8`, `04215fd`, `7f85241`, `397d859`, `05080a2` |
| `go.sum` — i2 | 10 commits, 2026-10-28 → 2026-11-26 | Every entry moves with `go.mod` and none independently: the same dependency bumps, the same three toolchain moves. No commit touches `go.sum` alone | Stable — it is `go.mod`'s shadow; its history is `go.mod`'s history | `ffbf163`, `f1895d6`, `298d459`, `4313e79`, `61914fd`, `2dab9fd`, `dc63861`, `effad08`, `c10b835`, `1275498` |
| `docs` (whole directory) | 25 commits, 2026-11-16 → 2026-11-27 | Before 2026-11-27 the only entries are release notes, typo fixes and the 1.17/1.18/1.19 patch notes; the 2026-11-27 entries are this project's own artifact and guide commits and carry no claim about the system | — not read for the system; the burst is this process writing | `9ebb541`, `5b90dc4`, `b1fe75e`, `84d16e0`, `21da553`, `83002fe`, `131c28c`, `9bdd38e`, `c72f585`, `65471ae`, `ea9c027`, `afb2e0c`, `2a61738`, `79ede0e`, `da96edb`, `0e06a44`, `71bbf5b`, `ffbf163`, `0337c32`, `62d5128`, `298d459`, `cd15eb6`, `b7d4231`, `4313e79`, `12c6cbd` |
| `pkg/config` — i2 | 20 commits, 2026-03-10 → 2026-11-26 | The Configuration spec was extended in almost every recent commit and reworked in two: the workflow actor types became bindable to their own state store (`fcf8200`) three hours after actor types became bindable at all (`44df256`), which is the change that moved `actorStateStoreBindings` from a single resolved store to a per-type map; the `MCPServerResource` and `WorkflowAccessPolicy` feature gates were removed at GA (`53dba81`); `HotReload` was enabled by default (`f8d0f61`). Priority blocks, pull dispatch, staged operations and the fast path each added a spec block | Unstable | `fcf8200`, `44df256`, `958a210`, `a6975cf`, `46c815e`, `ce93de6`, `ea364f3`, `6f3a997`, `c8c52f1`, `35a729a`, `57951e8`, `53dba81`, `39c1ef7`, `f8d0f61`, `163259a`, `d68ce05`, `c581fa8`, `f2d6198`, `60cd667`, `b2d1592` |
| `pkg/api` — i2 | 20 commits, 2026-10-09 → 2026-11-26 | The recent history is corrections to access control and to the app-facing contract rather than new surface: four security fixes landed inside three weeks (percent-encoded path ACL bypass `c39b0ae`, the reserved executor actor `0aa92f4`, the public port narrowed to GET endpoints `4a023d1`, header-size cap and constant-time token compare `ee602ae`), and the trace context was repaired twice (`12c6cbd`, `2e4b02d` in the sibling area). Only two commits add a building block — search and vector (`04215fd`), binary store (`397d859`) | Unstable | `44df256`, `12c6cbd`, `7ee17e2`, `61914fd`, `2dab9fd`, `bd54d83`, `3342040`, `ee602ae`, `4a023d1`, `5d5ae9a`, `e407edc`, `dcb1b1e`, `04215fd`, `2c9b171`, `66eddb5`, `2263bb9`, `397d859`, `0aa92f4`, `c39b0ae`, `3759a59` |
| `pkg/messaging` — i2 | 20 commits, 2025-07-22 → 2026-10-29 | The service-invocation path has been reworked repeatedly for security and for stream correctness: caller-identity and namespace headers were added and then repaired (`649d790`, `d2d4329`), the ACL bypasses were closed twice (`b330104`, `c39b0ae`), hop-by-hop and stale `Content-Length` forwarding were fixed (`e701ac8`, `f7427a1`), and the internal gRPC proxy stopped relaying to other apps (`cfc9ca5`). The only structural change is the replacement of a deep `proto.Clone` with a shallow copy (`0fba2e7`) | Unstable | `cfc9ca5`, `d3858e4`, `8999ca5`, `c39b0ae`, `d2d4329`, `0832cbf`, `9de85fe`, `3be34e3`, `4d43893`, `b330104`, `e701ac8`, `f7427a1`, `4625b1c`, `0fba2e7`, `856e8b2`, `269ef8c`, `70a3c7e`, `1333540`, `649d790`, `84550b5` |
| `pkg/channel` — i2 | 15 commits, 2026-02-04 → 2026-10-09 | Reworked for correctness on the app boundary rather than extended: a shared middleware pipeline handler across concurrent HTTP requests was fixed (`33df9ba`), the singleton connection limit for the local app was removed (`3be34e3`), SSE header copying and response stream buffering were fixed (`b0ac679`, `5199801`), and the reminder-targeting bypass was closed (`3759a59`). One commit added the app-initiated gRPC actor callback stream (`a86c03b`) | Unstable | `33df9ba`, `3759a59`, `2e4b02d`, `ac88108`, `8fd31d1`, `3be34e3`, `a86c03b`, `c8463fc`, `b330104`, `e701ac8`, `f7427a1`, `42bfae5`, `5199801`, `856e8b2`, `b0ac679` |
| `pkg/resiliency` — i2 | 15 commits, 2025-03-12 → 2026-10-20 | Mostly timeout semantics corrected rather than policy shape changed: a policy timeout racing the operation now returns `DeadlineExceeded` (`bf24b90`), and a gRPC `DeadlineExceeded` and a `Canceled` under a timeout are each treated as the timeout (`9f63450`, `ad5770c`); the circuit-breaker gauge was made to follow state transitions (`663fb9c`); the default-policy template expansion was cached after profiling (`c5522ce`). Two building blocks added their component types (`04215fd`, `397d859`) | Consolidating — the policy model is fixed and the corrections are in how a timeout is classified | `04215fd`, `ad5770c`, `397d859`, `9f63450`, `bf24b90`, `45947f6`, `297b0b3`, `f192439`, `c5522ce`, `b334e5f`, `663fb9c`, `f2d6198`, `856e8b2`, `f668523`, `cf9e2ec` |
| `pkg/components` — i2 | 15 commits, 2024-12-16 → 2026-10-20 | Extended twice for new building blocks (search and vector `04215fd`, binary store `397d859`), and otherwise reworked around graceful shutdown: pub/sub messages were stopped from being NACKed during shutdown (`2b2d526`), subscriptions were made to pause and drain (`88a04dd`), and in-flight input binding events were stopped from being cancelled (`7fe7262`). Pluggable components gained a response body limit (`8b14a11`) and had concurrent dispatch restored (`3ca99b7`) | Consolidating — the registries are stable and the churn is in shutdown ordering | `04215fd`, `397d859`, `8b14a11`, `3ca99b7`, `ea364f3`, `88a04dd`, `2b2d526`, `856e8b2`, `02b550f`, `7fe7262`, `94615f5`, `e4b2cd6`, `cf9e2ec`, `3ddf21a`, `f0f6a03` |
| `pkg/diagnostics` — i2 | 15 commits, 2026-08-20 → 2026-11-12 | The recent churn is trace-context correctness and per-workflow metric additions: a duplicated `traceparent` in gRPC metadata (`464764b`), pub/sub tracestate and baggage propagation across both protocols (`2e4b02d`), baggage ordering (`e73f6ba`), and one meter's aggregations leaking into another (`717c3e3`). The workflow engine added five metric families of its own (`e77ac60`, `41ac782`, `f192439`, `b974ab6`) | Unstable | `e77ac60`, `41ac782`, `04215fd`, `397d859`, `e4b3a66`, `464764b`, `e73f6ba`, `717c3e3`, `2e4b02d`, `f192439`, `54bb0c5`, `2fb4f01`, `b974ab6`, `566665b`, `b6f6236` |
| `pkg/metrics` — i2 | 14 commits, 2023-08-17 → 2026-10-28 | Reworked for the scheduler and for restarts: etcd metrics were restored on the scheduler after a regression (`6ad2e88`), the in-flight and undeliverable job gauges were added on `/metrics/scaling` (`1275498`), duplicate Prometheus samples after a hot-reload restart were fixed (`ece6ec7`), and the endpoint was restricted to `GET` and `HEAD` (`070f52f`) | Consolidating — three of the four recent commits correct a regression rather than add surface | `1275498`, `070f52f`, `ece6ec7`, `6ad2e88`, `f2d6198`, `80e5106`, `78343f1`, `cf9e2ec`, `8864e29`, `19cd93d`, `eb49e56`, `58edd57`, `328da65`, `b6540a1`, `605a7c3` |
| `pkg/healthz` — i2 | 7 commits, 2024-06-27 → 2026-08-21 | Two rewrites and little else. The package was replaced wholesale in 2024-06 (`eb49e56`, "Healthz overhaul") and gained named targets in 2025-06 (`78343f1`); since then only the listen-address flag was honoured (`f2d92bc`) and the Go and lint bumps. The most recent structural change in a neighbouring area — the placement client refactor (`91df244`) — touched its use, not its code | Consolidating | `f2d92bc`, `b4a8b94`, `856e8b2`, `91df244`, `78343f1`, `c900e90`, `eb49e56` |
| `pkg/apphealth` — i2 | 10 commits, 2025-03-10 → 2026-03-02 | Concentrated in one week of 2025-03 when the status handling was settled: the rate limiter was removed (`dc3b73b`), the status became a bool and was reported only on change (`e96251b`, `5d5919d`, `5d7f85f`), and the tests followed. One later functional change — a health transition stops reminders (`7f2b5e0`) — reached it from the workflow engine | Consolidating — no change for eight months except the Go bump | `856e8b2`, `7f2b5e0`, `dc3b73b`, `ec21527`, `5d7f85f`, `cf9e2ec`, `1a7a38c`, `5d5919d`, `e3c0ebf`, `e96251b` |
| `pkg/middleware` — i2 | 10 commits, 2019-12-06 → 2026-10-09 | The contract has not changed since the configurable HTTP pipeline landed in 2019 (`ba49ad5`); the later commits added the component kind (`f0dd0f5`, `e842111`), made the pipeline reloadable (`3eef69f`), and fixed one handler being shared by concurrent requests (`33df9ba`) | Stable | `33df9ba`, `3eef69f`, `e842111`, `9fbdf3a`, `f0dd0f5`, `3fbb932`, `23f2de8`, `1c145e0`, `9341e3f`, `ba49ad5` |
| `pkg/ports` — i2 | 3 commits, 2023-11-03 → 2026-04-20 | Nothing was reworked: the three commits are a `dapr/kit` update and two unrelated test-flake and workflow fixes that touched it in passing | Stable | `58fc395`, `2e4aff6`, `5e9dc0c` |
| `pkg/modes` — i2 | 9 commits, 2019-07-19 → 2021-12-01 | Nothing since 2021-12, and nothing before it but licence headers, a linter pass and the original name change. The two constants have not been added to or removed | Stable | `3fbb932`, `23f2de8`, `9341e3f`, `c10a2c3`, `a3ca79f`, `e1f8000`, `3fbd7d0`, `10406f3`, `90b3b2f` |
| `pkg/cors` — i2 | 5 commits, 2020-10-19 → 2025-10-07 | The package was created when CORS became optional (`4164950`) and changed once functionally since — the default filter was disabled (`42b7d73`). The other three are licence and linter passes | Stable | `42b7d73`, `3fbb932`, `23f2de8`, `9341e3f`, `4164950` |
| `img` — i2 | 10 commits, 2019-10-03 → 2025-03-25 | The diagrams were added and replaced in the repository's first three weeks (`2c4269a`, `6db6e12`, `64b8fc5`, `6cd8463`, `bd37004`) and the logo a month later (`1526f05`). The only change in five years is a README edit that touched the folder (`c3d9f19`, 2025-03) | Consolidating — frozen assets | `c3d9f19`, `dbc3d7f`, `db54b1e`, `9116115`, `1526f05`, `6cd8463`, `64b8fc5`, `6db6e12`, `bd37004`, `2c4269a` |
| Root documents — `README.md`, `CONTRIBUTING.md`, `GOVERNANCE.md`, `LICENSE`, `CODEOWNERS`, `RELEASE.md`, `SECURITY.md` | Commits not read | — | Not read — their content was read for the Perimeter and the Documentation grading; no commit log was listed for them, so nothing is claimed about how they changed | — |
| `pkg/proto` | Commits not read | — | Not read — generated code; its history is `dapr/proto`'s history | — |

## Anomalies
Twenty-three anomalies, each cited to the file it was read from. Nothing here is a judgement of quality: each row states what does not hold together, the anchor that shows it, and the risk it carries for whoever reads the system next.

```plantuml
@startuml
title Anomalies recorded through Survey iteration 2 - by area, by kind
skinparam componentStyle rectangle
skinparam wrapWidth 230

package "What the system declares about itself" {
  [AN-001 decision-record index\nlinks a record the tree does not hold] as A1
  [AN-002 dapr/README.md omits\n2 of the 9 proto packages] as A2
  [AN-005 decision records dormant\nsince 2023-09-11] as A5
  [AN-006 RELEASE.md and SECURITY.md\nare one-line pointers out of the tree] as A6
  [AN-011 no in-tree document states\nhow any of the six executables behaves] as A11
  [AN-013 2 of the 9 proto packages\ncarry no README] as A13
  [AN-021 the workflow engine README\ndescribes an older engine] as A21
  [AN-022 two index documents omit\nthe entries they index] as A22
}

package "The proto contracts and the generated path" {
  [AN-003 four documents state four\nprotoc versions, none of them the one that ran] as A3
  [AN-004 generate.sh reads proto inputs\nthe tree does not hold] as A4
  [AN-014 scheduler failure-policy types\nwith no .proto behind them] as A14
  [AN-015 the committed output names two\nprotoc versions, one of them not a release line] as A15
  [AN-017 hand-written Go committed\ninside the generated tree] as A17
}

package "The request path" {
  [AN-012 gRPC allowlist is a hand-maintained\nduplicate of the contracts] as A12
  [AN-023 the public listener gets a fresh\nrouter and three of the eight middlewares] as A23
}

package "The component plane and the control planes" {
  [AN-008 two workflow suites\ndisabled by renaming] as A8
  [AN-009 one component shim\ndisabled by renaming] as A9
  [AN-018 one scheduler process, two authorities,\nsettled by per-host capability flags] as A18
  [AN-019 one test file for 156\ncomponent registrations] as A19
}

package "The delivery surface and the pipeline" {
  [AN-007 70 version-skew patches keep\nolder release lines green] as A7
  [AN-010 tests/ is 2.5x pkg/\nand still unread] as A10
  [AN-016 the licence scan can report\nbut cannot fail the pipeline] as A16
  [AN-020 test setup reads cluster CIDRs\nout of an error message] as A20
}

note right of A18
  Three anomalies sit on an entry point:
  AN-012 on EP-002, AN-018 on EP-006,
  AN-023 on EP-003. The rest sit on a
  document, a generated file or a test
  artefact.
end note
note bottom of A23
  The public listener is a second HTTP router
  over the same handler functions. Its endpoint
  set is narrowed to the GET-only public list;
  its middleware is not.
end note
note bottom of A21
  The workflow engine's README is the one
  in-tree document describing a pkg/ area.
  Both of its stale claims are contradicted by
  directories in the same tree: the retentioner
  actors and the workflow integration suites.
end note
@enduml
```

| ID | Anomaly | Kind | Evidence | Likelihood | Impact |
|---|---|---|---|---|---|
| AN-001 | The decision-record index links a record the delivery does not hold: `ARC-004-http-server.md` is listed under Architecture, and `docs/decision_records/architecture/` holds three records. The index offers 24 links; the folder holds 23 records. A reader following the index reaches a 404. | Missing from the delivery | `docs/decision_records/decision_records.md`, § Dapr decision record organization and index (Architecture list, fourth entry) against `docs/decision_records/architecture/` (3 files: ARC-001, ARC-002, ARC-003) | likely | moderate |
| AN-002 | The proto package overview in `dapr/README.md` describes seven packages while the tree holds nine: `scheduler/` and `workflows/` are absent from the table. The two omitted packages carry the scheduler contract the actor-reminder cutover depends on and the MCP server contract, and both are declared in this inventory's entry points (EP-006, and the MCP server external system). | Document contradicted by the code | `dapr/README.md`, § Overview (a 7-row table) against `dapr/proto/scheduler/v1/{scheduler,placement}.proto` and `dapr/proto/workflows/v1/mcp.proto` | likely | low |
| AN-003 | Four documents each claim to be the proto generation path and each states a different `protoc` version: `tools/proto/generate.sh` pins 3.10.0, `dapr/README.md` names v25.4, `dapr/proto/runtime/v1/README.md` names v4.25.4, and `.github/copilot-instructions.md` names v34.1 and adds three plugin versions. Regenerating the committed bindings from any one of them does not reproduce the others, and the committed headers state two further versions again (AN-015). | Document contradicted by the code | `tools/proto/generate.sh`, § the `VERSION=` assignment; `dapr/README.md`, § Proto client generation; `dapr/proto/runtime/v1/README.md`, § Proto client generation; `.github/copilot-instructions.md`, § Protobuf / gRPC (Required tools) | likely | moderate |
| AN-004 | The documented client-generation script reads proto inputs the tree does not hold and writes into sibling repositories: its `generate()` loop reads `pkg/proto/dapr/dapr.proto` and `pkg/proto/daprclient/daprclient.proto`, while `pkg/proto/` holds generated `.pb.go` files and no `.proto`, and the definitions live under `dapr/proto/`. It also generates the JS, Java, Python and .NET SDKs into `../<language>-sdk/`, which are not part of this delivery. | Document contradicted by the code | `tools/proto/generate.sh`, § generate() (the `for proto_file in ...` loop) and § Download and install tools; against `pkg/proto/` and `dapr/proto/` | likely | moderate |
| AN-005 | No decision record has been added since 2023-09-11 (`c338385`) although decisions of the first rank landed after it: scheduler-hosted actor placement with a cutover and rollback (`3d9dbe6`, 2026-09-21), the search and vector building blocks (`04215fd`, 2026-10-20), MCP server resources, and workflow schedules (`61914fd`, 2026-11-11). The system's own statement of its architecture stops three years before its architecture did. | Missing from the delivery | Commit log of `docs/decision_records/` (last substantive commit `c338385`, 2023-09-11; last commit `62d5128`, 2026-11-25, a typo fix) against the `cmd` and `dapr` commit logs | likely | moderate |
| AN-006 | The release process and the security-issue policy are not in the delivery. `RELEASE.md` is a single link into `dapr/community` and `SECURITY.md` is a single link into `docs.dapr.io`, so how a release is cut and how a vulnerability is reported, triaged and disclosed cannot be read from the tree that produces the release. | Missing from the delivery | `RELEASE.md` (one line); `SECURITY.md`, § Security Policy (one link) | likely | moderate |
| AN-007 | Compatibility with older releases is kept by patching the old release's test tree rather than by adapting the current one: `.github/scripts/version-skew-test-patches/` holds 70 patch files across `release-1.12` to `release-1.18`, split between `dapr-sidecar-master` and `control-plane-master`. Each new release line adds a folder of patches that must keep applying. | Exceptional entity | `.github/scripts/version-skew-test-patches/e2e/` (18 files in 5 release folders) and `.../integration/` (52 files in 6 release folders) | likely | moderate |
| AN-008 | Two test files are disabled by renaming rather than deleted or fixed: `actors_test.go-TODO` and `wfengine_test.go-TODO` sit beside the suites they belong to. The rename removes them from the build silently and leaves no record of what they assert or why they stopped passing. | Suspicious coding style | `pkg/runtime/wfengine/backends/actors/actors_test.go-TODO`; `pkg/runtime/wfengine/wfengine_test.go-TODO` — tree evidence; the package is read in iteration 3 | possible | moderate |
| AN-009 | One component shim is disabled by renaming: `bindings_kitex.go_disabled` sits among the 156 component registrations, so the Kitex binding is neither registered nor removed. A reader counting components from the directory counts one that does not build. | Suspicious coding style | `cmd/daprd/components/bindings_kitex.go_disabled` | possible | low |
| AN-010 | The test substrate is larger than the code it tests and is the delivery's weight centre: `tests/` holds 3 335 files against `pkg/`'s 1 330 and `cmd/`'s 183, and the integration suite alone is 2 073 files. Reading it is the phase's overrun risk; the cut assigns it to iteration 4. | Exceptional entity | Repository tree: `tests/` (3 335 files), `tests/integration/suite/` (2 073 files), `pkg/` (1 330 files), `cmd/` (183 files) | likely | moderate |
| AN-011 | No in-tree document says how any of the six executables behaves. Five of them carry a three-line README that redirects to `docs.dapr.io`; `cmd/scheduler/` carries none, and it is the process that took both the reminder and the actor-placement authority. Behaviour a reader of this repository needs — what the scheduler's placement mode does, when the injector declines to inject, what placement's leadership does — is only on a documentation site outside the delivery. | Missing from the delivery | `cmd/daprd/README.md`, `cmd/injector/README.md`, `cmd/operator/README.md`, `cmd/placement/README.md`, `cmd/sentry/README.md` (each a redirect); `cmd/scheduler/` (no README; the directory holds `main.go`, `app/` and `options/` only) | likely | moderate |
| AN-012 | The gRPC API allowlist is a hand-maintained duplicate of the proto contracts. `pkg/api/grpc/endpoints.go` holds a literal map of some 120 fully-qualified method strings, grouped by API name and version, and nothing generates it from `dapr/proto/`. An RPC added to a `.proto` but not to this map cannot be named in a Configuration's `api` allowlist or denylist, so it silently escapes the access control the operator set; an RPC removed from a `.proto` leaves a dead entry that matches nothing. | Suspicious coding style | `pkg/api/grpc/endpoints.go` (`var endpoints = map[string][]string{…}`) against `dapr/proto/runtime/v1/dapr.proto`; consumed by `apiAccessRuleToMap` in the same file and by `getMiddlewareOptions` in `pkg/api/grpc/server.go` | likely | moderate |
| AN-013 | Two of the nine proto packages carry no README while the other seven do: `dapr/proto/scheduler/v1/` and `dapr/proto/workflows/v1/`. They are the two newest packages — the placement cutover and the MCP server contract — so the notes exist for exactly the packages whose semantics are settled and are missing for the ones a reader most needs explained. | Missing from the delivery | `dapr/proto/{common,components,internals,operator,placement,runtime,sentry}/v1/README.md` (7 files) against `dapr/proto/scheduler/v1/` (`scheduler.proto`, `placement.proto`) and `dapr/proto/workflows/v1/` (`mcp.proto`) | likely | low |
| AN-014 | A scheduler failure-policy type family in the committed bindings has no definition in the delivery. `pkg/proto/scheduler/v1/failurepolicy.pb.go` declares `dapr.proto.scheduler.v1.FailurePolicy`, `FailurePolicyDrop` and `FailurePolicyConstant`, generated from `dapr/proto/scheduler/v1/failurepolicy.proto` — a file the tree does not hold. The scheduler contract in use does not reference them: `scheduler.proto` imports `dapr/proto/common/v1/common.proto` and types `Job.failure_policy` as `common.v1.JobFailurePolicy`, so two parallel failure-policy families exist in one package and only one has a source. | Missing from the delivery | `pkg/proto/scheduler/v1/failurepolicy.pb.go` (header `source: dapr/proto/scheduler/v1/failurepolicy.proto`, and the `FailurePolicy`/`FailurePolicyDrop`/`FailurePolicyConstant` declarations) against `dapr/proto/scheduler/v1/` (2 files: `scheduler.proto`, `placement.proto`); `dapr/proto/scheduler/v1/scheduler.proto`, § `message Job` and the import block; `dapr/proto/common/v1/common.proto`, § `JobFailurePolicy` | likely | moderate |
| AN-015 | The committed generated tree records two different `protoc` versions in its own headers, so it cannot have been produced by one run of one toolchain. `pkg/proto/scheduler/v1/failurepolicy.pb.go` names `protoc v5.29.3`; `pkg/proto/common/v1/common.pb.go` and `pkg/proto/workflows/v1/mcp.pb.go` name `protoc v7.34.1`, all three sharing `protoc-gen-go v1.32.0`. Neither version appears in any of the four documents that state a version (AN-003), and `v7.34.1` matches none of them in any form. | Document contradicted by the code | `pkg/proto/scheduler/v1/failurepolicy.pb.go`, header `// protoc v5.29.3`; `pkg/proto/common/v1/common.pb.go` and `pkg/proto/workflows/v1/mcp.pb.go`, header `// protoc v7.34.1`; against the four version statements listed in AN-003 | likely | moderate |
| AN-016 | The licence-compliance scan can report but cannot fail the pipeline. `.github/workflows/fossa.yml` runs `fossas/fossa-action` to upload a scan, and the step that would act on the result — `run-tests: true` — is commented out with the note that it had been failing on FOSSA server issues, with the comment left in place: "VIEW RESULTS IN FOSSA PORTAL INSTEAD". The API key the job uses is written into the workflow file in plain text, annotated as a push-only token that is safe to expose. A dependency-licence failure therefore reaches no gate and no reviewer. | Inferior quality | `.github/workflows/fossa.yml`, § the `fossa-scan` job — the `FOSSA_API_KEY` env assignment and the commented-out "Run FOSSA Test" step | likely | moderate |
| AN-017 | Hand-written Go is committed inside the generated tree. Alongside the 66 `*.pb.go` files, `pkg/proto/` holds `proto.go`, `runtime/v1/dapr_additional.go`, `runtime/v1/dapr_tracing.go`, `runtime/v1/dapr_tracing_test.go`, `internals/v1/service_invocation_additional.go` and `internals/v1/service_invocation_additional_test.go` — files no protoc invocation produces and that a regeneration of the directory would overwrite. Their names state they are additions to generated types; nothing in the tree states which generation step preserves them. | Suspicious coding style | `pkg/proto/proto.go`, `pkg/proto/runtime/v1/{dapr_additional.go,dapr_tracing.go,dapr_tracing_test.go}`, `pkg/proto/internals/v1/{service_invocation_additional.go,service_invocation_additional_test.go}` — tree evidence; the six files were not opened in either iteration | possible | moderate |
| AN-018 | One scheduler process serves two authorities, and the split between them is negotiated at runtime rather than fixed by deployment. With `--placement-enabled` it answers `ReportActorTypes` as the actor-placement leader in addition to holding the durable job and reminder store; every host advertises `supports_pull_slots`, `scheduler_placement_enabled`, `leader` and `supports_scheduler_placement` on the stream, and a mixed-version cluster is kept on a single placement authority by withholding the advertisement while any connected sidecar or any probed placement service lacks the capability. Two behaviour sets, one process, one etcd store, and the authority in force is a property of what the connected peers report — not of the configuration alone. | Exceptional entity | `dapr/proto/scheduler/v1/scheduler.proto`, § `service Scheduler` (`ReportActorTypes`), § `message Host` (`leader`, `scheduler_placement_enabled`, `supports_pull_slots`), § `message WatchJobsRequestInitial` (`supports_scheduler_placement`, `placement_addresses`, `pull_slots`, `inflight_reminders`); `cmd/scheduler/options/options.go`; `pkg/scheduler/server/` (72 files) | likely | moderate |
| AN-019 | One test file covers 156 component registrations. `cmd/daprd/components/` holds 156 files that register every in-tree component implementation, and exactly one of them is a test: `pubsub_pulsar_test.go`. No unit test in the directory states that a registration compiles into the registry, so the build tags the package depends on (`allcomponents`, `stablecomponents`, `zz_notag.go` panicking otherwise) are exercised only when a whole binary is built. Whether the behaviour of a component is covered at all is stated in `tests/integration/`, which no iteration of this phase has read yet. | Missing from the delivery | `cmd/daprd/components/` (156 files; `pubsub_pulsar_test.go` the only `_test.go`); `cmd/daprd/components/zz_notag.go`; `cmd/daprd/app/app.go` (registry chaining) | likely | moderate |
| AN-020 | Test harness setup derives the cluster's service CIDR by reading an error message. `tests/setup_tailscale.sh` applies a Service with `clusterIP: 1.1.1.1` and extracts the real CIDR by `sed 's/.*valid IPs is //'` from whatever `kubectl` prints back, and derives the pod CIDR by grepping `kubectl cluster-info dump` for `cluster-cidr`. Both are free-text parses of another tool's output: a change in either message breaks the setup silently or with an unreadable failure. | Suspicious coding style | `tests/setup_tailscale.sh`, § the `POD_CIDR` and `SERVICE_CIDR` assignments | likely | low |
| AN-021 | The workflow engine's README describes an engine that no longer exists, in the two places a reader would rely on most. It states that "there is no automatic purging of state for completed workflows", and that "there are no end-to-end tests that directly target the Dapr Workflows engine yet". The tree holds the retention half of that answer — `pkg/actors/targets/workflow/retentioner/` (2 files: `factory.go`, `retentioner.go`), and the `stateRetentionPolicy` block of the workflow spec (`AnyTerminal`, `Completed`, `Failed`, `Terminated`) which `validateWorkflowSpec` parses — and 836 integration files under `tests/integration/suite/daprd/workflow/` plus `tests/e2e/workflows/`. A maintainer following this README purges state by hand and concludes the engine has no test coverage. | Document contradicted by the code | `pkg/runtime/wfengine/README.md`, § State storage ("IMPORTANT") and § Manual testing; against `pkg/actors/targets/workflow/retentioner/` (2 files); `pkg/config/configuration.go` (`WorkflowStateRetentionPolicy`, `validateWorkflowSpec`); `tests/integration/suite/daprd/workflow/` (836 files); `tests/e2e/workflows/` (8 files) | likely | moderate |
| AN-022 | Two index documents omit the entries they exist to index. `docs/development/README.md` links five guides while its own directory holds six — `preview-features.md`, the only in-tree statement of how a feature flag is declared, toggled and removed at GA, is absent. `charts/dapr/README.md` introduces the chart as installing the CRDs, the operator, the sidecar injector, Sentry, Placement and the Scheduler — six items — while `charts/dapr/charts/` holds seven subcharts, the seventh being `dapr_config`, whose single template renders the default Configuration resource the sidecar reads. In both cases the missing entry is the one a reader would not otherwise know to look for. | Document contradicted by the code | `docs/development/README.md` (5 links) against `docs/development/` (six guides); `charts/dapr/README.md`, § Chart Details against `charts/dapr/charts/dapr_config/templates/dapr_default_config.yaml` | likely | low |
| AN-023 | The public API listener is a second HTTP router over the same handler functions, and it is wired with a strict subset of the pipeline the primary listener gets. `StartNonBlocking` builds the primary router and applies to it `useMaxBodySize`, `useContextSetup`, `useTracing`, `useMetrics`, `useCors`, `useAPIAuthentication`, `useComponents` and `useAPILogging`; for the public port it builds a fresh router and applies only `useContextSetup`, `useTracing`, `useMetrics` and the routes. The endpoint list is narrowed to `PublicEndpoints()`, so the surface is smaller — but a request on the public port reaches those handlers with no request-body ceiling, no CORS policy, no API-token check and no component middleware. A deployment that sets a public port to expose the GET surface therefore exposes it without the body limit and the token check the operator configured for the main port. | Suspicious coding style | `pkg/api/http/server.go`, § `StartNonBlocking` — the primary block applying all eight middlewares against the `if s.config.PublicPort != nil` block applying three; `pkg/api/http/endpoints/endpoint.go` (`PublicEndpoints`) | possible | high |

## Priorities
Ordered by two things only: what the code concentrates its weight in, and what the reading itself needs before the next area can be stated. A rank is a reading order, not a value judgement — an area ranked twelfth is unread for that reason alone.

```plantuml
@startuml
title Reading order - thirteen ranks, and what each one is for
skinparam componentStyle rectangle
skinparam wrapWidth 240

package "The contracts everything else is read against" {
  [1  dapr/proto/\n9 services read, message shape remains] as P1
}

package "The request path and the code it runs through" {
  [2  cmd/daprd/\nthe registry and the runtime config] as P2
  [3  pkg/runtime/\n308 files, the largest library] as P3
  [4  pkg/api/ and pkg/diagnostics/\nthe operations and what instruments them] as P4
}

package "The stateful and control-plane machinery" {
  [5  pkg/actors/] as P5
  [6  pkg/scheduler/\nthe newest control plane] as P6
  [7  pkg/placement/\nthe one it supersedes] as P7
  [8  pkg/sentry/ and pkg/security/\nthe trust domain] as P8
  [9  pkg/operator/, pkg/injector/, apis/, client/] as P9
}

package "The delivery surface" {
  [10 .github/workflows/\nthe gates the delivery is held to] as P10
  [11 charts/ and docker/\nwhat ships] as P11
  [12 tests/\n3 335 files, the overrun risk] as P12
}

package "Legible only through their callers" {
  [13 the helper layer\ninternal/, utils/, the small pkg/ packages] as P13
}

note bottom of P4
  pkg/diagnostics holds the tracing and monitoring
  modules behind every dapr_* metric. Nothing in it is
  read yet; it is ranked with the request path it
  instruments, because a monitoring module is legible
  only against the operation it measures.
end note
note right of P11
  charts/dapr/README.md is read and its option tables
  match values.yaml, but its chart-details list omits
  dapr_config (AN-022). The templates and the eight
  Dockerfiles are unread.
end note
@enduml
```

| Order | Area | Why it comes first | Anchor |
|---|---|---|---|
| 1 | `dapr/proto/` | The contracts declare every operation the system offers, and every other area is read against them. Iteration 1 read all nine service definitions and closed the Perimeter on them; what remains is message shape — the `common/v1` types every request reuses, which the Use-Case Model needs to state a flow — and the two packages whose README is missing (AN-013). The package is also where the delivery is least coherent: two failure-policy families for one field (AN-014) and two `protoc` versions in the committed output (AN-015). | `dapr/proto/` (45 files); the nine service definitions read, listed in Coverage; `dapr/proto/common/v1/common.proto` and `dapr/proto/scheduler/v1/scheduler.proto` read in full |
| 2 | `cmd/daprd/` | The primary executable: `app.go` fixes the registry the 156 shims register into and the `runtime.Config` every knob arrives through. The shims answer which components exist at all, 155 of them are unread, and the directory carries one test file for the whole registration surface (AN-019). | `cmd/daprd/app/app.go`; `cmd/daprd/components/` (156 files, 1 `_test.go`) |
| 3 | `pkg/runtime/` | The largest library subtree (308 files) and the one the others bind together: hot reload, compstore, the component processor, the scheduler client and `wfengine/`. Every control plane's effect arrives here, two of its suites are disabled by renaming (AN-008), and its README is the one document in the tree that describes a `pkg/` area and has gone stale (AN-021). Three of its 308 files have been opened: two to settle a Persistent Data claim and the workflow engine README, and `mcpservers.go` to anchor where MCP server resources are loaded. | `pkg/runtime/` (308 files; 3 read); `pkg/runtime/runtime.go`; `pkg/runtime/wfengine/` (62 files) |
| 4 | `pkg/api/` and `pkg/diagnostics/` | The request path itself, with the instrumentation that measures it. Both iterations read the two servers, the route construction, the allowlist map and the errors package — enough to name the entry points and to see that the public listener receives a subset of the middleware pipeline (AN-023), but not to state what an operation does. `pkg/api/universal/` (29 files) is where each operation is actually implemented, and the allowlist map read here is the anomaly that reaches EP-002 (AN-012). `pkg/diagnostics/` (35 files) holds the OpenTelemetry and Zipkin exporters and one monitoring module per building block; none of it is read, because every module is legible only against the operation it instruments, and `docs/development/dapr-metrics.md` already names the source file of each metric group. Its 20 most recent commits are dominated by access-control corrections rather than new surface. | `pkg/api/` (124 files; 6 read); `pkg/api/universal/` (29 files, unread); `pkg/diagnostics/` (35 files, unread); `docs/development/dapr-metrics.md` |
| 5 | `pkg/actors/` | The stateful abstraction with its own table, placement client, reminders and workflow targets (243 files); the placement and scheduler cutover is argued over the semantics of this package. | `pkg/actors/` (243 files) |
| 6 | `pkg/scheduler/` | A separate process with its own etcd, leadership, placement authority and concurrency gates (76 files), and the newest control plane: it took the reminder and job authority from the sidecar and then placement from the placement service. It is the one process whose behaviour depends on what its peers report rather than on its configuration alone (AN-018), so it is read before the placement service it supersedes. | `pkg/scheduler/` (76 files); `cmd/scheduler/app/app.go`, `cmd/scheduler/options/options.go`; `dapr/proto/scheduler/v1/scheduler.proto` |
| 7 | `pkg/placement/` | The older control plane it replaced, still carrying Raft leadership and dissemination (26 files); the cutover cannot be read without it. | `pkg/placement/` (26 files) |
| 8 | `pkg/sentry/` and `pkg/security/` | The trust domain every other component depends on to talk at all: SVID issuance, trust bundles, JWT and OIDC validation, and the mTLS options the gRPC servers attach. | `pkg/sentry/` (36 files), `pkg/security/` (13 files) |
| 9 | `pkg/operator/`, `pkg/injector/`, `apis/`, `client/` | How a Dapr resource becomes a running sidecar: the reconcile loop, the admission patch and the CRD type surface. | `pkg/operator/` (38 files), `pkg/injector/` (29 files), `apis/` (46 files), `client/` (41 files) |
| 10 | `.github/workflows/` | The gates the delivery is held to — 25 workflows naming the build, test, version-skew, e2e and release steps, and the 70 version-skew patches the current test tree depends on (AN-007). Unread, and it is where the tree's own quality claims are either kept or not: the one workflow read shows a compliance gate that reports and cannot fail (AN-016). | `.github/workflows/` (25 files), `.github/scripts/` (82 files) |
| 11 | `charts/` and `docker/` | The delivery surface: seven subcharts and eight Dockerfiles are what actually ships, and the Deployment Model's trigger has fired on them. The chart's README is read and its option tables match `values.yaml`, but its chart-details list names six of the seven subcharts (AN-022); the templates and the Dockerfiles are unread. | `charts/dapr/` (67 files; 1 read), `docker/` (22 files) |
| 12 | `tests/` | The only empirical statement of behaviour in the tree, and its largest area (3 335 files). Read last because it is read against the systems under test. Its layout README is read; the suites are not. | `tests/` (3 335 files), `tests/integration/suite/` (2 073 files) |
| 13 | The helper layer — `internal/`, `utils/`, the eleven small `pkg/` packages | These expose no entry point of their own; they are legible only once their callers are known. Iteration 2 read `pkg/{config,modes,ports,cors,apphealth,healthz,metrics}` in full or in substance, and the contract of `pkg/{components,resiliency,middleware,messaging,channel}` — which is why this rank stays last: each is a contract the callers explain. | `internal/` (32 files), `utils/` (9 files); `pkg/modes` (1 file), `pkg/cors` (1), `pkg/ports` (2), `pkg/middleware` (7), `pkg/apphealth` (3), `pkg/healthz` (8), `pkg/metrics` (6), `pkg/resiliency` (13), `pkg/components` (53), `pkg/messaging` (16), `pkg/channel` (14), `pkg/config` (30) |

## Coverage
Files opened against files in the tree, per area. An area at zero is declared unread, not skimmed. Each read count is the number of files enumerated after it on the same row, so every count here reproduces from its own enumeration. A row marked *outside a slice* holds a file opened because a perimeter fact, an anomaly's evidence or a comparison could not be closed without it; the reason is in its last column rather than counted against any slice area. `i1` and `i2` name the iteration that opened the row.

Iteration 1 was assigned 13 areas holding 620 files and opened 48 distinct files in them, which is the sum of the thirteen i1 rows below (3 + 1 + 1 + 1 + 1 + 1 + 1 + 1 + 12 + 13 + 9 + 1 + 3). It also opened 12 files outside its slice, in 7 areas: `tools` 2, `.build-tools` 1, `pkg/api` 5, `pkg/channel` 1, `pkg/healthz` 1, `pkg/injector` 1, `tests` 1. Iteration 1 therefore opened 60 distinct files.

Iteration 2 was assigned 16 areas holding 317 files. It opened 19 files those areas had not yielded before, which is the sum of the fifteen yielding i2 rows (1 + 3 + 1 + 2 + 1 + 1 + 1 + 1 + 1 + 1 + 1 + 1 + 2 + 1 + 1 — the `docs` row counting `setup-ci.md` plus the three re-opened guides), re-opened 3 that iteration 1 had first opened (`pkg/api` 1, `pkg/channel` 1, `pkg/healthz` 1), and opened 7 further files outside its slice: `.build-tools` 1, `pkg/runtime` 3, `pkg/responsewriter` 1, `charts` 1, `tests` 1. Iteration 2 therefore opened 26 distinct files it had not opened before. Fifteen of its 16 assigned areas yielded a file; `pkg/diagnostics` is declared unread.

Across the Survey reading so far: **86 distinct files opened of the 6 631 entries the tree holds** — 60 from iteration 1 and 26 from iteration 2. Every gRPC service the delivery declares is read from its definition; the message bodies, the component shims, the test substrate and the request-path implementations are not. Twenty-three of the areas Technology and Size lists were never assigned to a slice and are declared unread at the foot of this table.

| Area | Files | Read | Unread and why |
|---|---|---|---|
| `.github` — i1 | 120 | 3 — `copilot-instructions.md`, `pull_request_template.md`, `workflows/fossa.yml` | 117 unread: 24 of the 25 workflow definitions and all 82 helper scripts state behaviour in code rather than about it; they are priority 10 and belong to a later iteration. `fossa.yml` was opened because it is the one workflow whose contents bear on a gate the delivery claims to hold (AN-016). The 9 issue templates were read only as a directory listing. |
| `CODEOWNERS` — i1 | 1 | 1 — `CODEOWNERS` | — |
| `CONTRIBUTING.md` — i1 | 1 | 1 — `CONTRIBUTING.md` | — |
| `GOVERNANCE.md` — i1 | 1 | 1 — `GOVERNANCE.md` | — |
| `LICENSE` — i1 | 1 | 1 — `LICENSE` | — |
| `README.md` — i1 | 1 | 1 — `README.md` | — |
| `RELEASE.md` — i1 | 1 | 1 — `RELEASE.md` | — |
| `SECURITY.md` — i1 | 1 | 1 — `SECURITY.md` | — |
| `cmd` — i1 | 183 | 12 — `daprd/README.md`, `injector/README.md`, `operator/README.md`, `placement/README.md`, `sentry/README.md`, `daprd/main.go`, `daprd/app/app.go`, `daprd/components/zz_notag.go`, `operator/app/app.go`, `scheduler/app/app.go`, `scheduler/options/options.go`, `injector/app/app.go` | 171 unread: 155 of the 156 component shims, every `options.go` except the scheduler's, `placement/app/app.go`, `sentry/app/app.go`, and the three tool drivers. A shim is read where a component's behaviour is in question, not as a sweep. |
| `dapr` — i1 | 45 | 13 — `README.md`, `proto/runtime/v1/dapr.proto`, `proto/runtime/v1/appcallback.proto`, `proto/operator/v1/operator.proto`, `proto/sentry/v1/sentry.proto`, `proto/placement/v1/placement.proto`, `proto/scheduler/v1/scheduler.proto`, `proto/scheduler/v1/placement.proto`, `proto/internals/v1/service_invocation.proto`, `proto/components/v1/state.proto`, `proto/common/v1/common.proto`, `proto/components/v1/README.md`, `proto/internals/v1/README.md` | 32 unread: the message bodies of `runtime/v1` outside `dapr.proto` and `appcallback.proto` (`actors`, `ai`, `state`, `pubsub`, `workflow`, `jobs`, `search`, `vector`, `binarystore`, `crypto`, `lock`, `metadata`, `configuration`, `secret`, `binding`, `invoke`), the four remaining component contracts, and the five remaining package READMEs. Every service the tree declares is read from its definition; what remains is message shape, which the Use-Case Model needs and the Perimeter does not. |
| `docs` — i1, extended i2 | 198 | 10 — `README.md`, `CONTRIBUTING.md`, `decision_records/decision_records.md`, `development/README.md`, `development/developing-dapr.md`, `development/dapr-metrics.md`, `development/preview-features.md`, `development/setup-ci.md`, `release_notes/template.md`, `release_notes/v1.19.3.md`; three of the guides were re-opened at i2 to grade them first-hand, and `setup-ci.md` was opened for the same reason | 188 unread: the 23 decision-record bodies, 151 further release notes, the two remaining development guides (`setup-dapr-development-env.md`, `setup-dapr-development-using-vscode.md`), the 5 screenshots and the debug-flow image, and `artifacts/` (six files this process wrote, not part of the system). The decision-record bodies are architecture evidence for Excavation. |
| `go.mod` — i1 | 1 | 1 — `go.mod` | — |
| `pkg/proto` — i1 | 66 | 3 — `scheduler/v1/failurepolicy.pb.go`, `common/v1/common.pb.go`, `workflows/v1/mcp.pb.go` | 63 unread: committed generated code. Three were opened for the generation header every file carries and for the failure-policy family that has no `.proto` (AN-014, AN-015, AN-017); the other 63 are read only where a handler's behaviour is in question. |
| `tools` — outside a slice, i1 | 4 | 2 — `proto/generate.sh`, `boilerplate.go.txt` | The reason these were opened, and it is not a slice: `generate.sh` is cited by `dapr/README.md` and `docs/CONTRIBUTING.md` as the proto-generation path (AN-003, AN-004), and the boilerplate file was opened to check the licence-header claim in `.github/copilot-instructions.md`. `codegen.mk` and `tools.go` unread. |
| `.build-tools` — outside a slice, i1 and i2 | 14 | 2 — `cmd/root.go` (i1), `README.md` (i2) | The reason, and it is not a slice: it is the seventh Go entry point (EP-014) and a separate module (`dapr-build-tools`), which is why the component table lists it apart from the six in `cmd/`. Its README was opened at i2 to grade it as documentation. Twelve unread: the four drivers and their three test fixtures. |
| `pkg/api` — assigned at i2, outside a slice at i1 | 124 | 6 — `http/http.go`, `http/server.go`, `http/endpoints/endpoint.go`, `grpc/server.go`, `grpc/endpoints.go` (i1, with `http/server.go` re-opened at i2), `errors/README.md` (i2) | 118 unread, including all of `universal/` (29 files), which is priority 4. At i1 the five files were opened for the listener count, the route families, the two gRPC servers and the hand-maintained allowlist (AN-012); at i2 the errors README was opened as documentation of the slice and `server.go` was re-opened for the public-port and profiling listeners EP-003 and EP-013 rest on, and for the middleware each of them receives (AN-023). |
| `pkg/channel` — assigned at i2, outside a slice at i1 | 14 | 2 — `http/http_channel.go` (i1, re-opened i2), `channel.go` (i2) | 12 unread: the gRPC channel, the fake and the testing doubles. The app channel is the boundary to the application workload; the interface carries the operations it offers, which is what the external-actors row states. |
| `pkg/healthz` — assigned at i2, outside a slice at i1 | 8 | 2 — `server/server.go` (i1, re-opened i2), `healthz.go` (i2) | 6 unread: the target lifecycle, `target.go`, the fake and the tests. The server is the anchor for EP-011 and the interface is what a target registers against. |
| `pkg/injector` — outside a slice, i1 | 29 | 1 — `service/handler.go` | The reason, and it is not a slice: the admission webhook is an entry point (EP-008), and this file holds the decision to decline or patch. 28 unread: the 14-file patcher and the remaining `service/` files. |
| `tests` — outside a slice, i1 and i2 | 3 335 | 2 — `setup_tailscale.sh` (i1), `README.md` (i2) | The reason, and it is not a slice: the first is the pipeline's evidence of how the test cluster is provisioned, which is where AN-020 comes from; the second was opened at i2 to grade it as documentation. 3 333 unread — the phase's overrun risk (AN-010) and priority 12. |
| `go.sum` — i2 | 1 | 1 — `go.sum` | — Read to check the one claim `go.mod` cannot settle: that the resolved module graph is closed. It is a checksum list and states no behaviour. |
| `img` — i2 | 3 | 3 — `dapr_logo.svg`, `overview.png`, `dapr_conceptual_model.jpg` | — Read to settle whether any of the three states a fact about the perimeter. None does: the banner is a logo and the two diagrams restate `README.md`. |
| `pkg/messaging` — i2 | 16 | 2 — `v1/messaging.go`, `direct_messaging.go` | 14 unread: `v1/`'s request, response and replay types, and the gRPC proxy. The interface and the direct-messaging entry point were read to fix the shape of the service-invocation path the allowlist duplicates (AN-012). |
| `pkg/components` — i2 | 53 | 1 — `category.go` | 52 unread: the 15 component-category registries and their tests. The category list fixes the 15 kinds a component can be, which is what the external-systems rows need; a registry is read with the processor that calls it. |
| `pkg/resiliency` — i2 | 13 | 1 — `resiliency.go` | 12 unread: the breaker, retry and policy implementations and their tests. The provider interface, the built-in policy names and the target types are what the component row states. |
| `pkg/middleware` — i2 | 7 | 1 — `middleware.go` | 6 unread: the HTTP pipeline, the component store and their tests. The `HTTP` type is the whole contract the `middleware.http` component kind implements. |
| `pkg/config` — i2 | 30 | 1 — `configuration.go`, opened and re-opened within i2 to settle `System Inventory#F1` | 29 unread: the ACL trie, the mode and protocol helpers, and the 19 testdata fixtures. This file is where the actor-state binding rules are enforced and where the preview features are declared. |
| `pkg/diagnostics` — i2 | 35 | 0 | Unread in full: the 13 monitoring and tracing modules, their helpers and their tests. Nothing this iteration needed could not be closed from `docs/development/dapr-metrics.md`, which names the source file of every metric group; the modules themselves are read with the request path they instrument (priority 4). |
| `pkg/metrics` — i2 | 6 | 1 — `exporter.go` | 5 unread: the meter, the options and their tests. The exporter is the `/metrics` endpoint, so it is the anchor for EP-012. |
| `pkg/ports` — i2 | 2 | 1 — `ports.go` | 1 unread — `ports_test.go`. The stable-port derivation is how a standalone sidecar's ports are chosen; the package exposes no entry point of its own. |
| `pkg/modes` — i2 | 1 | 1 — `modes.go` | — Two constants, `kubernetes` and `standalone`. This is the whole package. |
| `pkg/apphealth` — i2 | 3 | 1 — `health.go` | 2 unread — `status.go` and `health_test.go`. The probe loop is what makes the app-health signal an input to the sidecar's readiness. |
| `pkg/cors` — i2 | 1 | 1 — `cors.go` | — Two constants, the empty default origin and `*`. This is the whole package. |
| `pkg/runtime` — outside a slice, i2 | 308 | 3 — `actorstatestores.go`, `wfengine/README.md`, `mcpservers.go` | The reason, and it is not a slice: `System Inventory#F1` cites a symbol in `actorstatestores.go`, and a citation into a file the inventory declares unread is not an anchor, so the file was opened to settle what it declares. The workflow engine README is the one in-tree document describing a `pkg/` area, and it is contradicted by the code (AN-021). `mcpservers.go` was opened because the MCP server external system needed an anchor naming where those resources are loaded. 305 unread; the area is priority 3 and iteration 3's slice. |
| `pkg/responsewriter` — outside a slice, i2 | 3 | 1 — `README.md` | The reason, and it is not a slice: it was the only `pkg/` README not yet graded, and it is where a forked third-party library is recorded. 2 unread: `response_writer.go` and its test. |
| `charts` — outside a slice, i2 | 67 | 1 — `dapr/README.md` | The reason, and it is not a slice: it is the document for the area priority 11 carries, and the only in-tree statement of what the chart deploys. Its option tables match `values.yaml` and its chart-details list omits the seventh subchart (AN-022). 66 unread: the 7 subcharts, the 7 CRDs and the templates. |
| `docker` — never assigned | 22 | 0 | Unread in full: eight Dockerfiles, `docker.mk`, the five custom scripts and the five library scripts. No slice has reached it; it is priority 11 with `charts/`. |
| `grafana` — never assigned | 6 | 0 | Unread in full: five dashboard definitions and a README. They restate the metric names `docs/development/dapr-metrics.md` already carries, so nothing in them could change the Perimeter. |
| `pkg/acl` — never assigned | 10 | 0 | Unread in full: the service-invocation ACL and the eight-file workflow policy extractor. Its rules are configurable through `pkg/config`'s ACL trie, which is where the reading reached it. |
| `pkg/apis` — never assigned | 46 | 0 | Unread in full: the seven resource kinds, their deep-copy code and the one conversion. The kinds are named in Persistent Data and in EP-015 from the tree listing; their field surface is priority 9. |
| `pkg/backoff` — never assigned | 2 | 0 | Unread in full: a jitter helper and its test. Nothing in the Perimeter rests on it. |
| `pkg/buildinfo` — never assigned | 2 | 0 | Unread in full: the build-time version and feature values. Reached only through `LoadFeatures`, which `pkg/config` calls. |
| `pkg/client` — never assigned | 41 | 0 | Unread in full: the generated clientset, informers and listers. Generated code for the kinds priority 9 carries. |
| `pkg/encryption` — never assigned | 4 | 0 | Unread in full: state-store envelope encryption. It sits under the state component path, which is priority 4 and 5. |
| `pkg/expr` — never assigned | 2 | 0 | Unread in full: one expression helper and its test. |
| `pkg/internal` — never assigned | 32 | 0 | Unread in full: the disk and Kubernetes resource loaders and the two validators. Referenced as an anchor for EP-015 and EP-016 from the tree listing; the loaders themselves are priority 9. |
| `pkg/messages` — never assigned | 5 | 0 | Unread in full: the legacy error set and the error-code table. `pkg/api/errors/README.md` describes how the newer model sits beside it. |
| `pkg/operator` — never assigned | 38 | 0 | Unread in full: the operator's API server, informer loops, pod-metadata cache and watchdog. Priority 9. |
| `pkg/outbox` — never assigned | 3 | 0 | Unread in full: the outbox that makes a publish idempotent across a state transaction. Read with the pub/sub pipeline, priority 3. |
| `pkg/placement` — never assigned | 26 | 0 | Unread in full: Raft leadership, dissemination and the actor table. Priority 7. |
| `pkg/retry` — never assigned | 1 | 0 | Unread in full: one retry helper and its test. |
| `pkg/scopes` — never assigned | 2 | 0 | Unread in full: the resource-scope matcher. Its behaviour is visible only through the operator resource it filters, priority 9. |
| `pkg/security` — never assigned | 13 | 0 | Unread in full: SPIFFE, workload certificates, mTLS options, the API token and JWT handling. Priority 8. |
| `pkg/sentry` — never assigned | 36 | 0 | Unread in full: the certificate authority, the trust bundle and the JWT and OIDC validators. Priority 8. |
| `pkg/sse` — never assigned | 1 | 0 | Unread in full: one server-sent-events helper. |
| `pkg/testing` — never assigned | 26 | 0 | Unread in full: the mock substrate. It states what the tests expect, not what the system does, so it is read with `tests/` at priority 12. |
| `pkg/validation` — never assigned | 2 | 0 | Unread in full: one validation helper and its test. |
| `swagger` — never assigned | 1 | 0 | Unread in full: the generated OpenAPI description of the HTTP API. Generated from the route table EP-001 already cites. |
| `utils` — never assigned | 9 | 0 | Unread in full: the cross-package helpers for host, HTTP middleware, Kubernetes and DNS. Legible only through their callers, priority 13. |

## Counts

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
| EP-015 | — | — | — |
| EP-016 | — | — | — |
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
| AN-012 | — | DependsOn | EP-002 |
| AN-013 | — | — | — |
| AN-014 | — | — | — |
| AN-015 | — | — | — |
| AN-016 | — | — | — |
| AN-017 | — | — | — |
| AN-018 | — | DependsOn | EP-006 |
| AN-019 | — | — | — |
| AN-020 | — | — | — |
| AN-021 | — | — | — |
| AN-022 | — | — | — |
| AN-023 | — | DependsOn | EP-003 |

An entry point has no upstream: it is the first element the reading produces, and every use case recovered from it will cite it. An anomaly has no upstream either — its source is the evidence cited in its own row — except where it sits on an entry point, and the three that do carry `DependsOn` the entry point whose reading exposed them: AN-012 on EP-002, the gRPC surface whose allowlist the map duplicates; AN-018 on EP-006, the scheduler contract whose capability flags negotiate the two authorities; AN-023 on EP-003, the public listener whose middleware pipeline is the subset. AN-021 and AN-022 are anomalies of documents rather than of an entry point, so neither carries a link.

