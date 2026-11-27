## Document Control
Phase: Survey
Status: Draft — iteration 1. Twenty-five risks scored: the twenty anomalies of the System Inventory are each carried as a risk, beside five risks of the phase's shape. Three are High, thirteen Significant, nine Moderate; all sixteen Significant and High are on avoid or transfer, so no stakeholder grant is outstanding
Milestone Target: end-of-Survey milestone (SBC) — not yet achieved

## Risk Classification
Every risk is scored on the IARI risk scale (Development Case §9.10 / baseline). This Risk List defines no scale of its own: probability is 1–5, impact is 1–5, exposure is their product, and the magnitude is the band that exposure falls in — never a band chosen for it.

| Probability | Level | Means |
|---|---|---|
| 1 | Very low | 10 % or less |
| 2 | Low | 11 % to 30 % |
| 3 | Medium | 31 % to 50 % |
| 4 | High | 51 % to 70 % |
| 5 | Very high | More than 70 % |

| Impact | Level | Means |
|---|---|---|
| 1 | Very low | No deviation from the plan worth recording |
| 2 | Low | Extra work inside the current iteration; its objectives are still met |
| 3 | Medium | An iteration objective slips to a later iteration the phase already plans |
| 4 | High | The milestone slips: the phase needs an iteration it did not plan |
| 5 | Very high | Success is endangered: a declared requirement or constraint can no longer be met as declared |

| Magnitude | Exposure | Whose grant to accept it |
|---|---|---|
| High | 15–25 | The stakeholder's |
| Significant | 9–12 | The stakeholder's |
| Moderate | 5–8 | Ours — avoidance, transfer or acceptance with mitigation and contingency |
| Minor | 3–4 | Ours |
| Low | 1–2 | Ours |

```plantuml
@startuml
title Risk List structure - how a risk's magnitude and strategy are derived
hide empty members
skinparam wrapWidth 200

class "Risk" as RISK <<register entry>> {
  id : RNNN
  description
  probability : 1..5
  impact : 1..5
  exposure : probability x impact
  owner : agent role
  strategy : avoid | transfer | accept
  mitigation
  contingency
  trigger
}

class "Magnitude" as MAG <<enumeration>> {
  Low : 1..2
  Minor : 3..4
  Moderate : 5..8
  Significant : 9..12
  High : 15..25
}

class "Strategy" as STRAT <<enumeration>> {
  avoid
  transfer
  accept
}

class "StakeholderGrant" as GRANT <<record>> {
  risk id
  the stakeholder's own words
}

class "Anomaly" as ANOM <<System Inventory>> {
  id : AN-NNN
  source anchor
}

RISK "1" --> "1" MAG : magnitude is the band\nits exposure falls in
RISK "1" --> "1" STRAT : chosen per risk
RISK "1" --> "0..1" GRANT : required only when the\nmagnitude is Significant or High\nand the strategy is accept
ANOM "0..*" --> "0..1" RISK : a scored risk refines\nthe anomaly that exposed it

note right of MAG
  The scale is the Development Case's
  (IARI baseline section 9.10). This
  Risk List defines no scale of its own.
end note

note bottom of GRANT
  Acceptance hands the consequence to the
  stakeholder, so it is theirs to grant.
  No role records it on their behalf.
  This register holds no Significant or
  High risk on accept, so it holds no grant.
end note

note bottom of ANOM
  In Survey every risk about the system
  comes from an anomaly the reading recorded.
  A risk of the phase's own shape has no
  AN-NNN and cites a Development Case
  section or a repository path instead.
end note
@enduml
```

### Distribution after this iteration

```plantuml
@startmindmap
title Risk List after Survey iteration 1 - 25 risks, by magnitude and strategy
* Risk List - 25 risks
** High 15 to 25 - 3, all avoided
*** R011 architecture statement dormant since 2023 - 16
*** R016 no in-tree account of executable behaviour - 16
*** R023 one scheduler process, two authorities - 16
** Significant 9 to 12 - 13, all avoided
*** R001 unequal cut, tests holds 3335 entries - 12
*** R005 repository files read as orders - 9
*** R006 an empty size filled with a unit nothing measures - 9
*** R009 four protoc versions, none the one that ran - 12
*** R010 the documented generator reads absent inputs - 12
*** R012 release and security procedure outside the delivery - 12
*** R013 70 version-skew patches must keep applying - 12
*** R014 two workflow suites disabled by renaming - 9
*** R017 gRPC allowlist is a hand-maintained duplicate - 12
*** R019 failure-policy family with no proto behind it - 12
*** R020 committed bindings name two protoc versions - 12
*** R021 licence scan reports and cannot fail - 12
*** R022 hand-written Go inside the generated tree - 12
** Moderate 5 to 8 - 9
*** accepted, mitigation and contingency written - 3
**** R002 SBC gate at the 14-day queue ceiling - 8
**** R003 versions read as policy rather than evidence - 6
**** R025 test setup parses an error message for CIDRs - 8
*** avoided - 6
**** R004 decision records renumbered into project families - 6
**** R007 index links a record the tree does not hold - 8
**** R008 proto overview omits two packages - 8
**** R015 one shim disabled by renaming - 6
**** R018 two proto packages carry no README - 8
**** R024 one test file for 156 registrations - 8
** Minor 3 to 4 - none
** Low 1 to 2 - none
@endmindmap
```

The three High risks are about one thing: the delivery states less about itself than its code does. R011 is its architecture statement stopping in 2023; R016 is its behaviour living on a site outside the tree; R023 is its newest control plane taking its authority from what its peers report. Excavation reads the code and cites the anchors — that is the avoidance for all three, and for the thirteen Significant risks beside them: each is avoided by the shape of the reading the next phase is given, not by a promise made to the stakeholder.

## Risk Register
Twenty-five risks. Twenty refine an anomaly the Software Archaeologist recorded; five are risks of the phase's own shape with no anomaly behind them. The `Source` cell cites the anomaly id and the anchor it was read from; the trace edge is in `## Traceability`.

| ID | Risk | Source | P | I | Exposure | Magnitude | Strategy | Owner |
|---|---|---|---|---|---|---|---|---|
| R001 | The cut is unequal. `tests/` holds 3 335 of the tree's 6 629 entries and sits with `charts/`, `docker/`, `swagger/` and `platforms/` in Survey iteration 4's slice; a slice that overruns turns one iteration into two, and the phase then needs an iteration the cut did not plan. | AN-010; repository tree | 4 | 3 | 12 | Significant | Avoid | Project Manager |
| R002 | The SBC gate is the phase's only human dependency: three reviewer verdicts with no role working between them. Beyond the 14-day ceiling the process suspends rather than filling the gap, and the milestone waits for however long the queue is. | Development Case § Measurement policy | 2 | 4 | 8 | Moderate | Accept | Project Manager |
| R003 | The tree fixes its toolchain (`go 1.26.11`, exact pseudo-versions for the `dapr/*` modules). A reading that states those as the project's policy pre-empts a decision recorded in Inception, and the statement must then be re-read. | Development Case § Version policy | 3 | 2 | 6 | Moderate | Accept | Project Manager |
| R004 | The 23 records under `docs/decision_records/` are the system's own architectural evidence. A role may renumber them into the project's `API-*`/`ARC-*` families, or treat a record as an instruction to this process. | Development Case § Which artifact carries recovered material | 3 | 2 | 6 | Moderate | Avoid | Project Manager |
| R005 | The repository carries instruction-shaped files — `.github/copilot-instructions.md`, the nine issue forms, `CONTRIBUTING.md`, the release procedure. Read as orders rather than as evidence of how the system is worked, they take a role outside the declared scope. | Scope guard, sprint zero | 3 | 3 | 9 | Significant | Avoid | Project Manager |
| R006 | No phase of this project has closed, so no token spend and no elapsed time is measured and every work item's size is empty. Filled with a person-week or an invented token forecast, the plan reads as measured and is not. | Development Case § Measurement policy | 3 | 3 | 9 | Significant | Avoid | Project Manager |
| R007 | The decision-record index offers 24 links against the 23 files the folder holds: a reader following it reaches a 404, and a recovery that counts architecture records from the index counts one that does not exist. | AN-001; `docs/decision_records/decision_records.md` | 4 | 2 | 8 | Moderate | Avoid | Project Manager |
| R008 | `dapr/README.md` lists seven proto packages while the tree holds nine: the two it omits carry the scheduler placement contract and the MCP server contract, both of them entry points of this inventory. | AN-002; `dapr/README.md` | 4 | 2 | 8 | Moderate | Avoid | Project Manager |
| R009 | Four documents each claim to be the proto generation path and each states a different `protoc` version — 3.10.0, v25.4, v4.25.4, v34.1 — and none of them produced the committed bindings. A regeneration from any one is not reproducible from the others. | AN-003; `tools/proto/generate.sh`, `dapr/README.md`, `dapr/proto/runtime/v1/README.md`, `.github/copilot-instructions.md` | 4 | 3 | 12 | Significant | Avoid | Project Manager |
| R010 | The documented client-generation script reads `pkg/proto/dapr/dapr.proto` and `pkg/proto/daprclient/daprclient.proto`, files the tree does not hold, so the generation path Excavation would cite cannot run at all. | AN-004; `tools/proto/generate.sh` | 4 | 3 | 12 | Significant | Avoid | Project Manager |
| R011 | No decision record has been added since 2023-09-11 while decisions of the first rank landed — scheduler-hosted actor placement with cutover and rollback, search and vector, MCP server resources, workflow schedules. A recovery that takes the record set as its authority recovers a system three years older than the code. | AN-005; commit log of `docs/decision_records/` | 4 | 4 | 16 | High | Avoid | Project Manager |
| R012 | The release process and the vulnerability-disclosure policy are not in the delivery: `RELEASE.md` is one link into `dapr/community` and `SECURITY.md` one link into `docs.dapr.io`, so how a release is cut and how a defect is disclosed cannot be read from the tree that produces the release. | AN-006; `RELEASE.md`, `SECURITY.md` | 4 | 3 | 12 | Significant | Avoid | Project Manager |
| R013 | Compatibility with older releases is kept by patching the old release's test tree: 70 patch files across `release-1.12` to `release-1.18`, and each new release line adds a folder that must keep applying. | AN-007; `.github/scripts/version-skew-test-patches/` | 4 | 3 | 12 | Significant | Avoid | Project Manager |
| R014 | Two workflow test suites are disabled by renaming — `actors_test.go-TODO`, `wfengine_test.go-TODO` — which removes them from the build silently and leaves no record of what they assert or why they stopped passing. | AN-008 | 3 | 3 | 9 | Significant | Avoid | Project Manager |
| R015 | One component shim is disabled by renaming: `bindings_kitex.go_disabled` neither registers nor disappears, so a component count taken from the directory counts one that does not build. | AN-009; `cmd/daprd/components/bindings_kitex.go_disabled` | 3 | 2 | 6 | Moderate | Avoid | Project Manager |
| R016 | No in-tree document states how any of the six executables behaves. Five carry a three-line redirect out of the tree, `cmd/scheduler/` carries none, and that is the process which took both the reminder and the actor-placement authority. | AN-011; `cmd/*/README.md` | 4 | 4 | 16 | High | Avoid | Project Manager |
| R017 | The gRPC API allowlist is a hand-maintained map of some 120 fully-qualified method strings in `pkg/api/grpc/endpoints.go`, generated from nothing. An RPC added to a `.proto` but not to the map silently escapes the access control a Configuration declares over it. | AN-012; `pkg/api/grpc/endpoints.go` | 4 | 3 | 12 | Significant | Avoid | Project Manager |
| R018 | Two of the nine proto packages carry no README, and they are the two newest — the placement cutover and the MCP contract: the notes exist for the packages whose semantics are settled and are missing for the ones a reader most needs explained. | AN-013 | 4 | 2 | 8 | Moderate | Avoid | Project Manager |
| R019 | A `dapr.proto.scheduler.v1` failure-policy family is committed in the bindings with no `.proto` behind it, while the contract in use types `Job.failure_policy` as `common.v1.JobFailurePolicy`: two parallel families in one package, one of them sourceless, so a reader cannot tell which the scheduler honours. | AN-014; `pkg/proto/scheduler/v1/failurepolicy.pb.go` | 4 | 3 | 12 | Significant | Avoid | Project Manager |
| R020 | The committed generated tree names `protoc v5.29.3` in one file and `protoc v7.34.1` in others. No single toolchain run produced it, and neither version appears in any of the four documents that state one. | AN-015; `pkg/proto/scheduler/v1/failurepolicy.pb.go`, `pkg/proto/common/v1/common.pb.go` | 4 | 3 | 12 | Significant | Avoid | Project Manager |
| R021 | The licence-compliance workflow runs a scan whose acting step is commented out, with the key written in plain text beside it: a dependency-licence failure reaches no gate and no reviewer, and the delivery appears covered by a check it does not have. | AN-016; `.github/workflows/fossa.yml` | 4 | 3 | 12 | Significant | Avoid | Project Manager |
| R022 | Hand-written Go is committed inside the generated tree — `proto.go`, `runtime/v1/dapr_additional.go`, `dapr_tracing.go` and its test, two files under `internals/v1` — and nothing states which generation step preserves them; a regeneration overwrites them. | AN-017; `pkg/proto/` | 4 | 3 | 12 | Significant | Avoid | Project Manager |
| R023 | One scheduler process serves two authorities, and the split is negotiated at runtime rather than fixed by deployment: with `--placement-enabled` it answers `ReportActorTypes` as placement leader as well as holding the job and reminder store, and a mixed-version cluster is kept on one authority by withholding a capability advertisement. A reader who takes the authority from configuration misdescribes the process. | AN-018; `dapr/proto/scheduler/v1/scheduler.proto`, `cmd/scheduler/options/options.go` | 4 | 4 | 16 | High | Avoid | Project Manager |
| R024 | `cmd/daprd/components/` holds 156 registration files and exactly one test, so the build tags the package depends on (`allcomponents`, `stablecomponents`, `zz_notag.go` panicking otherwise) are exercised only when a whole binary is built. | AN-019; `cmd/daprd/components/` | 4 | 2 | 8 | Moderate | Avoid | Project Manager |
| R025 | Test harness setup derives the cluster's service and pod CIDRs by parsing free-text `kubectl` output; a change in either message breaks the setup silently or with an unreadable failure. | AN-020; `tests/setup_tailscale.sh` | 4 | 2 | 8 | Moderate | Accept | Project Manager |

Three risks are High (R011, R016, R023) and thirteen Significant. All sixteen are on `avoid`: the consequence is designed out of the work, so nothing is handed to the stakeholder. The three accepted risks — R002, R003, R025 — are Moderate, where acceptance with a written mitigation and contingency is this role's decision. **No risk is on `transfer`** — no third party has agreed to carry a consequence of this project — and **no stakeholder grant is outstanding.** This register records none.

## Risk Mitigation and Contingency
| R012 | The tree is recorded as not carrying either procedure: `RELEASE.md` and `SECURITY.md` are cited as one-line pointers and the external targets are named. Neither policy is reconstructed from inference, and no artifact claims the project holds a release or disclosure procedure. | Where later work needs the release or disclosure procedure, it is cited from `dapr/community` and `docs.dapr.io` as an external dependency of the delivery, and named in the Software Architecture Document as a boundary. Bringing the procedure in-tree is a Change Request, not a sprint-zero edit. | A phase artifact states how a release is cut or a vulnerability disclosed without naming the external source. |
## Traceability
| Element | Traces From | Link Type | Traces To |
|---|---|---|---|
| R001 | AN-010 | Refines | — |
| R002 | — | — | — |
| R003 | — | — | — |
| R004 | — | — | — |
| R005 | — | — | — |
| R006 | — | — | — |
| R007 | AN-001 | Refines | — |
| R008 | AN-002 | Refines | — |
| R009 | AN-003 | Refines | — |
| R010 | AN-004 | Refines | — |
| R011 | AN-005 | Refines | — |
| R012 | AN-006 | Refines | — |
| R013 | AN-007 | Refines | — |
| R014 | AN-008 | Refines | — |
| R015 | AN-009 | Refines | — |
| R016 | AN-011 | Refines | — |
| R017 | AN-012 | Refines | — |
| R018 | AN-013 | Refines | — |
| R019 | AN-014 | Refines | — |
| R020 | AN-015 | Refines | — |
| R021 | AN-016 | Refines | — |
| R022 | AN-017 | Refines | — |
| R023 | AN-018 | Refines | — |
| R024 | AN-019 | Refines | — |
| R025 | AN-020 | Refines | — |

Twenty of the twenty-five refine an anomaly the Software Archaeologist recorded, and every anomaly of the System Inventory now carries at least one. The five with no upstream — R002 to R006 — are risks of the phase's own shape: the human gate, the version policy, the decision records, the instruction-shaped files and the empty size. Each cites its Development Case section in its register row, because a development-case section is not a trace endpoint.

The edge between an anomaly and the entry point it sits on — AN-012 on EP-002, AN-018 on EP-006 — is written in the System Inventory, where those labels are declared. It is not repeated here.

