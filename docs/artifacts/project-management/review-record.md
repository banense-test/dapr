## Document Control
### Reviewer lens
<!-- review: Survey I3 -->

<!-- review: Survey I3 -->

Phase: Survey
Status: Draft — iteration 3 review, technical lens. Two prior findings of this lens closed (Development Case F1, System Inventory F4); two issued (Iteration Plan F1 Major, System Inventory F5 Minor). The SBC milestone verdict is the Review Coordinator's and is not issued here
Milestone Target: end-of-Survey milestone (SBC) — not yet achieved

### Management Reviewer lens
<!-- review: Survey I3 -->

Phase: Survey — iteration 3, cycle 1

Status: Draft — management lens, Iteration Acceptance review of Survey iteration 3. One Major finding re-recorded under its existing key and one Minor recorded. `System Inventory#F1` resolved. No Critical finding, so nothing escalates to the stakeholder.

Milestone Target: end-of-Survey milestone (SBC) — not judged by this review, not yet achieved

### Review Coordinator lens
<!-- review: Survey I3 -->

<!-- review: Survey I3 -->

Phase: Survey — iteration 3, cycle 1
Status: Draft — the four open findings of this review consolidated with owners, severity and criteria; iteration acceptance recorded, the phase iterates
Milestone Target: end-of-Survey milestone (SBC) — not judged by this review, not yet achieved
Review type: Iteration Acceptance. No phase sanction is sought and none is granted
Lenses: Technical (Reviewer) EXECUTED; Management (Management Reviewer) EXECUTED; Business (Business Reviewer) INACTIVE — did not evaluate this review
Open at this close: 2 Major, 2 Minor, 0 Critical
Actions: 4 — two hold the SBC gate, two are their owners' next turn

## Review Scope and Criteria
### Reviewer lens
<!-- review: Survey I3 -->

<!-- review: Survey I3 -->

Iteration Acceptance review of Survey iteration 3 from the technical lens. The review point is COMPLETION, not the milestone's exit criteria: the question is what each role did with the twelve areas the Project Manager cut for this iteration. The SBC verdict belongs to the Review Coordinator and is not stated here.

#### Artifacts reviewed

| Artifact | Owner | Discipline | Reviewed against |
|---|---|---|---|
| System Inventory | Software Archaeologist | Discovery | The anchors it cites, opened independently; its own Coverage, Technology and Size, Perimeter, Documentation and Traceability tables; the i3 slice |
| Iteration Plan | Project Manager | Project Management | Its own objectives O1–O6, its cut arithmetic, its risk placement, whether every figure reproduces |
| Risk List | Project Manager | Project Management | The baseline risk scale, whether the 27 anomalies are all carried, accountability |
| Development Case | Process Engineer | Environment | The IARI baseline; the §5.2 optional-trigger conditions and whether every cited path resolves |

No pull request exists to dispose of; the SCM holds none in any state. That is correct for Survey: no role of this phase produces code, and the Implementation Model does not exist yet.

#### The slice this review judges

The Project Manager assigned twelve areas in order: `pkg/scheduler`, `pkg/placement`, `pkg/runtime`, `pkg/actors`, `pkg/sentry`, `pkg/security`, `pkg/operator`, `pkg/apis`, `pkg/client`, `pkg/injector`, `pkg/acl`, `pkg/diagnostics`.

All twelve appear in the System Inventory's Coverage table. Eleven were read to a stated depth with the remainder declared; `pkg/injector` yielded no file of its own this iteration because the one file it was read from belongs to iteration 1 and was re-opened here. That is a correct declaration and not a finding.

`pkg/diagnostics` is the one place the slice does not hold. It was re-cut into this iteration expressly to be read against the operations the phase has now read, and the Coverage row reads one file of its thirty-five and sends the other thirty-four to "priority 4" — the System Inventory's reading order for Excavation. Neither of the two remaining iterations of the cut carries the area. That is F1 below, and it is a defect of the plan that cut it rather than of the reading, which declares its remainder honestly.

#### Criteria applied, and what each decided

| Criterion | Standard for this artifact type | Outcome |
|---|---|---|
| Evidence | Every fact about the existing system rests on a file that says what the artifact claims | Holds on all nine anchors opened independently. This is what F4's correction had to establish, and it does |
| Count reproducibility | A stated count equals the enumeration printed beside it | Fails on two rows of Technology and Size — F5 |
| Slice coverage | Every area of the assignment described, or its remainder declared into the cut | Fails on one area — F1, at the plan that cut it |
| Traceability | Every declared `EP-NNN` and `AN-NNN` has a row; `Traces To` is only `DependsOn` | Holds against the RTM: 30 links — 27 `Refines` from an `AN-NNN` to an `RNNN`, and 3 `DependsOn` from an `AN-NNN` to an `EP-NNN`. No copied edge, no invented label |
| Objectives met on the artifact's own evidence | O1 to O6 each met by an anchor, a label or a reconciled figure | O1–O4 and O6 met; O5 is not met on its own terms, which is the substance of F1 |
| No invented figure | No spend, size, velocity, density or projected date | Holds. Two iterations' measured actuals are reported as those iterations' own facts, each with both clocks apart |
| Optional triggers | Every FIRED verdict's §5.2 condition genuinely holds; every cited path resolves | Holds. Glossary FIRED on load-bearing specialist vocabulary; Deployment Model FIRED on seven subcharts, eight Dockerfiles, six executables, two first-class modes. The four NOT FIRED each fail their condition on a stated fact |
| Baseline conformance | No roster redefinition, no CORE omission or reassignment, no artifact outside the universe, no merged roles, no enumeration rule changed | Holds. Business Modeling INACTIVE with its trigger condition recorded; Capsule Designer withheld on `real-time-system = false`; nothing else touched |
| Stakeholder answer applied | The recorded answer is in the artifact, in their own terms | Holds. The pointer line is at the top of the root `CONTRIBUTING.md`, the owner's text below it unchanged |
| Instruction-shaped files recorded, not obeyed | A repository file stating a rule is evidence, never an order | Holds. `.github/copilot-instructions.md`, the issue forms and the owner's guide are read as evidence throughout |
| UML | Diagrams present, notation not invented, elements stable | Holds. Every diagram is PlantUML that validates; element labels are the declared identifier families |

#### Entry criteria

| Check | Outcome |
|---|---|
| The artifacts of the increment are present and readable in full | Met — all four read |
| The assignment is known and every area of it accounted for | Met — twelve assigned, twelve accounted for, one at a depth its own criterion does not accept |
| The upstream the review rests on is readable | Met — System Inventory read before the plan's counts were judged against it |
| Prior findings of this lens reconciled before new ones recorded | Met — Development Case#F1 and System Inventory#F4 closed in `S_RECONCILE` first, then F1 and F5 recorded |
| A pull request to dispose of | None — no role of Survey produces code |

#### What is deliberately not judged

- **The milestone.** Exit criteria are the Review Coordinator's. This review asks what the iteration did with its slice.
- **Another lens' findings.** `System Inventory#F1` was recorded by the Management Reviewer and is not mine to close. I read the Coverage table it names; I do not close it.
- **Depth inside a reached area.** Ordered to Excavation by the Priorities section, with the single exception F1 names — an area whose remainder has no iteration of the cut to land in.
- **Elements outside the slice.** A call named in a flow step, a component named by its path at a connector, a rule recorded as not yet checked against the code — all work the cut gives a later iteration, and none is a finding here.

### Management Reviewer lens
<!-- review: Survey I3 -->

Review type: Iteration Acceptance — the fourth review activity, applied to a sprint-zero iteration. The System Boundary Confirmed milestone is not judged here; the Project Manager's cut still holds 30 areas for the two iterations that follow, and the phase does not end on this verdict.

What this review asks, in the management lens, and nothing else:

| # | Question | Applied to |
|---|---|---|
| M1 | Did the iteration read what the cut gave it, and is every area it did not reach declared? | System Inventory; Iteration Plan's cut tables |
| M2 | Is every anomaly the reading recorded carried as a risk with its magnitude, its strategy and an accountable role? | Risk List; System Inventory's anomalies |
| M3 | Does every risk the plan says an iteration confronts name an area that iteration opens? | Iteration Plan's risk-driven sequencing; Risk List's sources |
| M4 | Does the plan carry any rate, work-item size, phase-length forecast or calendar date — a figure the process has not measured? | Iteration Plan; Risk List's `R006` |
| M5 | Does the register hold a Significant or High risk on accept without the stakeholder's grant, and is custody separated from accountability? | Risk List |
| M6 | Does the Development Case's tailoring contradict the inherited process? | Development Case |

The management lens does not re-open what the technical lens reviewed: anchors, code claims and stated counts are the Reviewer's. Where the two lenses meet the same fact, this block records its own finding only if the defect is the management one — a placement, a count read as evidence, or an unmeasured figure.

```plantuml
@startuml
title Compliance matrix - Survey iteration 3, management lens
hide empty members
skinparam classAttributeIconSize 0
skinparam wrapWidth 320
skinparam nodesep 26
skinparam ranksep 34

class "System Inventory\n(Discovery, Critical)" as SI {
  P  all 12 areas of the slice assigned and accounted for
  P  11 read to a stated depth, pkg/diagnostics declared at 1 of 35
  P  every read count reproduces: 2+2+9+2+1+1+2+4+1+0+2+1 = 27
  P  27 in-slice plus 3 outside = 30 distinct; 60+28+30 = 118 for the phase
  P  the three outside-slice files each state the perimeter fact that needed them
  P  every anchor names a file this inventory opened, or says in place that it did not
  P  the never-assigned statement now derives from the rows it counts
}

class "Iteration Plan\n(Project Management, Medium)" as IP {
  P  12 areas named in the order the Work Order gives
  P  no rate, no work-item size, no phase-length forecast, no calendar date
  P  two iteration actuals reported with both clocks apart, neither a rate
  P  R023 R019 R014 R024 R026 R029 R030 R031 R032 each name an area this slice opens
  P  R017 restated with no claim about universal/; R019 moved here; R028 on server.go
  F  R013 and R021 placed on i4, whose areas hold neither anomaly (F3)
  F  Document Control states an SBC verdict the same section denies (F4)
}

class "Risk List\n(Project Management, Medium)" as RL {
  P  scale is the baseline's, every magnitude is its exposure's band
  P  27 anomalies, 27 refining risks; R029 to R032 carry AN-024 to AN-027
  P  R023 scored High on a mixed 4x4 and avoided, accountable role named
  P  no Significant or High risk on accept; no stakeholder grant outstanding
  P  custody and accountability separate on all 32 rows
}

class "Development Case\n(Environment, High)" as DC {
  P  no delta stated this iteration contradicts the baseline
  P  the measurement policy the plan and the register are judged against is this document's
}

class "Legend" as L {
  P = pass
  F = fail, with the finding key
  scope = this iteration's 12 areas, in the order taken
}

note bottom of SI
  The management questions on this artifact are two: did the iteration
  read what the cut gave it, and did what it found become a risk.
  Both hold - 11 areas read to a stated depth, and the four anomalies
  this slice recorded are R029 to R032.
end note
note bottom of IP
  Two findings: F3 re-recorded, Major, the placement count;
  F4 recorded, Minor, one Document Control line. The three rows
  F3's remediation named are corrected; the count they were
  required to make true is not.
end note
@enduml
```

#### Entry criteria

| Material | State when this review opened |
|---|---|
| System Inventory | Read at its iteration-3 state: 12 areas, 27 in-slice files, 3 outside |
| Iteration Plan | Read at its iteration-3 state: 12 areas, the 32-risk sequencing, this iteration's fine plan |
| Risk List | Read at its iteration-3 state: 32 risks, five restated and three added this iteration |
| Development Case | Read for the deltas this iteration states; unchanged in its triggers |
| Iteration Assessment | Not consulted as evidence of this iteration. The Project Manager authors it after this verdict; its absence of iteration-3 content is not a finding |

### Review Coordinator lens
<!-- review: Survey I2 -->

<!-- review: Survey I2 -->

Iteration Acceptance review of Survey iteration 2, at the close of the sixteen-area slice the Project Manager cut. The event reviewed is COMPLETION of the iteration, not the SBC milestone: the question is what each role did with its assignment, not whether Survey may close.

#### The review event and its trigger

```plantuml
@startuml
title Survey iteration 2 - the review event, the lenses that ran, and the verdict path
skinparam wrapWidth 260

start
:Iteration 2 closes its slice\n16 areas assigned, 15 reached,\npkg/diagnostics declared unread in full;
:Entry criteria checked\nfour artifacts present and readable in full;\nthe assignment known and every area accounted for;
fork
:Technical lens - Reviewer\nEXECUTED;
note right
13 anchors re-opened independently,
10 held and 3 did not.
Three prior findings closed, two recorded.
end note
fork again
:Management lens - Management Reviewer\nEXECUTED;
note right
Three prior findings closed, two recorded.
Risk scale, risk placement and
accountability checked.
end note
fork again
:Business lens - Business Reviewer\nINACTIVE - did not evaluate this review;
note right
Business Modeling is INACTIVE in every
phase, so the role is withheld by the
tailoring. No verdict is attributed to it.
end note
end fork
:Consolidate the ledger\nartifact, key, recording lens, severity, criterion;
:Scan for cross-lens conflict\nkey collisions, overlapping defects,\nand any claim the increment contradicts;
if (a Critical finding is open?) then (yes)
:REQUIRES_USER_INPUT to the stakeholder\nnothing closes until they answer;
else (no - neither executing lens recorded one)
:Iteration Accepted - the phase iterates\nSBC milestone not judged, no sanction sought;
endif
:Next boundary - Survey i3\ncontrol planes and actors, 11 areas;
stop
@enduml
```

The event is keyed to the iteration boundary, not to a date: a slipped iteration slips its review with it. The next review event on the calendar is the Iteration Acceptance at the close of Survey i3, and the SBC milestone review opens only after the last iteration of the cut and only when every Major finding of the phase has its rework in the baseline.

#### Scope of record

| Element | Value |
|---|---|
| Iteration | Survey 2, cycle 1 — request path and component plane |
| Areas assigned | 16, in the order the Work Order names them |
| Areas reached | 15. `pkg/diagnostics` is declared unread in full with its reason and is not counted as reached |
| Files opened | 28 distinct this iteration; 88 across the phase |
| Artifacts reviewed | System Inventory, Development Case, Iteration Plan, Risk List |
| Artifact read, not assessed | Iteration Assessment — it records iteration 1 and says so; the Project Manager authors this iteration's after the verdict |
| Pull requests to dispose of | None. No role of Survey produces code and the SCM holds no PR in any state, so nothing is approved, changes-requested or deferred |
| Artifact outside this increment | None recorded. The Software Architecture Document is an LCA review target whose first phase is Excavation; nothing is recorded against it |

#### Entry criteria, as enforced

| Criterion | Standard | Outcome |
|---|---|---|
| Artifacts in target state, not draft fragments | Four artifacts present and readable in full | Met |
| Reviewers assigned with expertise matched to the artifact domain | Technical lens on all four; management lens on the plan, the register, the iteration's objectives and the tailoring | Met — both lenses executed on their own criteria; the Business lens is withheld by the Development Case, not mismatched |
| Scope fixed before evaluation, and the scope is the assignment | The Work Order's sixteen areas, in its order | Met — fifteen reached, one declared unread with its reason |
| Evaluation criteria distributed before evaluation | The plan's six objectives and eight criteria, the IARI risk scale, the Development Case measurement policy | Met — each lens states the criteria it applied |
| Prior findings of a lens reconciled before new ones recorded | The six findings earlier reviews left open taken first | Met — six closed, four recorded, none folded into another |
| Findings carry owner, severity and criterion at close | Every finding either closed or scheduled with an owner | Met — the four open findings each carry an action and an owner below |

## Findings
### Reviewer lens
<!-- review: Survey I3 -->

<!-- review: Survey I3 -->

Two findings this iteration: one Major against the Iteration Plan, one Minor against the System Inventory. Nothing is recorded against the Risk List or the Development Case from this lens — silence is the approval.

#### Iteration Plan

**F1 — Major — criterion `consumed-defect`.** `pkg/diagnostics` was re-cut into this iteration to be read here, and after one file of thirty-five it is deferred out of the cut entirely.

The plan's own Objective O5 states the reasoning: "`pkg/diagnostics/` is read — the area iteration 2 assigned and declared unread in full — and each module is stated against the operation this phase has read. A module that instruments an operation no iteration of this phase read is declared so rather than counted as reached." Iteration 2 deferred the area because a monitoring module is legible only against the operation it instruments; the re-cut into i3 exists to put it where the code it instruments is read.

The Coverage row then reads one file of thirty-five — `metrics.go`, the seven default meters — and sends the rest to "priority 4": "the thirteen monitoring and tracing modules, their helpers and their tests. ... The modules themselves are read with the request path they instrument (priority 4)." Priority 4 is the System Inventory's reading order for Excavation, not a Survey iteration.

Neither remaining iteration carries the area. Iteration 4's seventeen areas and iteration 5's thirteen do not list `pkg/diagnostics`, and the cut's seventy-area sum counts it once, here. So an area the gate reads as reached in this iteration has thirty-four of its thirty-five files leaving the phase with no iteration to land in.

The defect is the plan's, not the reading's. Reading one file and declaring the rest is the rule this phase follows and every other area of this slice follows it correctly. What fails is that this area's declared remainder names an order that is not part of the cut, which makes the deferral invisible to the SBC condition "the cut holds no unread area". This is the same failure class `Iteration Plan#F3` recorded last iteration — a placement resting on an area whose reading does not actually happen — recurring at the cut's arithmetic rather than in a risk row.

O5's exit criterion is not met on its own terms. No module is stated against an operation this phase read, and no module is declared as instrumenting an operation no iteration read — which O5 admits as the alternative outcome and would have been satisfied by.

*Remediation.* Two admissible outcomes, and the plan must state one of them explicitly. Either read the thirteen modules in this iteration against the operations W2–W4 recovered — the scheduler's eight operations, the placement leader gate, the Sentry validators, the actor table's suspension and the internal server's five handlers are all read now, so a module instrumenting any of them can be stated against it and one instrumenting none can be declared so — or state in the cut that `pkg/diagnostics` is carried to Excavation: name it in iteration 5's row or in a carried-to-Excavation line beside the cut, and drop it from this iteration's reached count so the SBC condition is not answered by a number that is short thirty-four files. Whichever is chosen, the Coverage row and the plan's reached count must agree.

#### System Inventory

**F5 — Minor — criterion `contained-defect`.** Two rows of Technology and Size state a file count that the enumeration printed on the same row does not yield.

The `docs/development` row reads "Six guides — setup, setup in VS Code, CI, developing, metrics, preview features — plus five screenshots and an image of the debug flow | 13". Its own enumeration is 6 + 5 + 1 = 12 against the 13 it states; the file that makes 13 is `docs/development/README.md`, which this same artifact's Documentation table records as read. The enumeration is short one rather than the count being wrong.

The `docker` row reads "Eight Dockerfiles — the runtime image, a debug image, a dev image, Mariner and five Windows base images — plus `docker.mk` and `library-scripts/` of five Debian provisioning scripts | 22". The prose names nine Dockerfiles against the eight it states — runtime, debug, dev, Mariner and five Windows — and 8 + 1 + 5 = 14 leaves eight of the stated 22 unaccounted.

Nothing another role builds on turns on either row. The Deployment Model trigger rests on the seven subcharts, the eight Dockerfiles, the six executables and the two runtime modes, and all of those hold: the Development Case names exactly eight Dockerfiles, four of them Windows. No objective of this iteration turns on either row and the cut is stated in areas, not in these counts. It is recorded because the Coverage introduction states that every count the artifact prints reproduces from the enumeration on its own row, and this is the same class `System Inventory#F2` closed in Coverage and `#F4` closed in the component row — corrected instance by instance rather than at the rule.

*Remediation.* Name `docs/development/README.md` among the files of the `docs/development` row so its enumeration yields the 13 it states. For `docker`, reconcile the statement with the enumeration: either the Windows set is four, which is what the eight named Dockerfiles give and what the Development Case's list implies, or the row is nine and must say which ninth file it means; then account for the remaining files of the 22 — the eight Dockerfiles, `docker.mk` and the five library scripts are 14, so seven more must be named. Then re-check the rest of the table the same way.

#### Compliance matrix

```plantuml
@startuml
title Compliance matrix - Survey iteration 3, technical lens
hide empty members
skinparam classAttributeIconSize 0
skinparam wrapWidth 320
skinparam nodesep 26
skinparam ranksep 34

class "System Inventory\n(Discovery, Critical)" as SI {
  P  all 12 areas of the i3 slice described, or declared at their read depth
  P  every Coverage row's stated count reproduces from its enumeration
  P  2+2+9+2+1+1+2+4+1+0+2+1 = 27 in-slice, plus 3 outside = 30; 60+28+30 = 118
  P  the scheduler's authority stated from api.go, not from a flag name
  P  ReportActorTypes read as the one-line forward to the placement subsystem
  P  component plane stated from registry.go, manager.go and category.go
  P  actor table read: Types vs ReminderTypes, drains, suspension
  P  Subscription conversion read: ConvertTo drops Routes.Rules
  P  clientset read: three group clients against seven served kinds
  P  every anchor names a file the inventory opened, or says in place it did not
  P  Traceability: 16 EP rows, 27 AN rows, 3 DependsOn, no copied edge
  P  every anomaly carries the file it was read from
  F  two Technology and Size rows state a count their enumeration does not yield (F5)
}

class "Iteration Plan\n(Project Management, Medium)" as IP {
  P  12 areas named in the order taken, the Work Order's order
  P  no work-item size, no rate, no phase-length forecast, no calendar date
  P  i1 and i2 restated at what they read, every area in exactly one iteration
  P  70 areas sum: 13 + 15 + 12 + 17 + 13
  P  32 risks: 27 placed, 5 carried by the reading as a whole
  P  every i3 placement names an area this iteration opens
  P  R017 and R019 restated, Iteration Plan#F3 answered at the plan's own end
  P  rework table names the iteration and work item that closes each finding
}

class "Risk List\n(Project Management, Medium)" as RL {
  P  scale is the baseline's, none invented
  P  every anomaly of the 27 carried by at least one scored risk
  P  accountable role named per row, distinct from the custodian
  P  no Significant or High risk on accept without a stakeholder grant
}

class "Development Case\n(Environment, High)" as DC {
  P  roster intact: 26 roles, none merged, none reassigned
  P  Business Modeling INACTIVE with its trigger condition stated
  P  no CORE artifact omitted, no ownership changed
  P  Glossary and Deployment Model FIRED, four NOT FIRED, each on its own condition
  P  every Evidence-column path now resolves in the tree
  P  measurement policy: two quantities, both clocks apart
}

class "Legend" as L {
  P = pass
  F = fail, with the finding key
  scope = this iteration's 12 areas, in the order taken
}

note bottom of SI
  Two findings, one Major: pkg/diagnostics was re-cut into
  this iteration to be read against the operations and 34 of
  its 35 files are deferred back to priority 4 (F4). The
  count defect is F5, Minor.
end note
note bottom of DC
  The two prior findings of this lens are closed on files
  opened for this review: pkg/config/modes/ resolves, and
  registry.go holds thirteen fields.
end note
@enduml
```

#### Anchors opened for this review

I do not accept an anchor on the artifact's word. These nine were read directly and compared against what the System Inventory, the Iteration Plan and the Development Case say they show:

| Anchor | What the artifact claims it shows | Outcome |
|---|---|---|
| `pkg/runtime/registry/registry.go` | thirteen registry fields, `nameresolution` among them, and no workflow field | Holds — thirteen typed fields plus `componentCb` and `reporter`; no `workflow` or `workflowbackend` |
| `pkg/runtime/processor/manager.go` | the twelve categories `category()` dispatches on | Holds — bindings, configuration, cryptoProvider, lock, middleware, pubsub, secretStore, stateStore, conversation, search, vector, binaryStore |
| `pkg/actors/table/table.go` | `Types()` advertised to placement against `ReminderTypes()` to the scheduler; suspension without a state store | Holds — both methods present; `Options.StartSuspended`, `SuspendHosting`, `ResumeHosting`, and `GetOrCreate` refusing while suspended |
| `pkg/apis/subscriptions/v2alpha1/conversion.go` | `ConvertTo` writes `Routes.Default` into `Route` and never reads `Routes.Rules` | Holds — `dst.Spec.Route = s.Spec.Routes.Default`; no read of `Rules` in either direction |
| `charts/dapr/crds/subscription.yaml` | both versions served, `v2alpha1` storage, conversion by webhook | Holds — `v1alpha1` `storage: false`, `v2alpha1` `storage: true`, `strategy: Webhook` |
| `pkg/client/clientset/versioned/clientset.go` | three group clients against the seven kinds the operator serves | Holds — `ComponentsV1alpha1`, `ConfigurationV1alpha1`, `MCPServerV1alpha1` only |
| `pkg/runtime/wfengine/README.md` | states no automatic purge and no workflow e2e tests, both contradicted by code read this iteration | Holds — both sentences present verbatim, so AN-021 is correctly recorded as a live contradiction |
| `pkg/config/modes/kubernetes_config.go`, `standalone_config.go` | the two per-mode configuration files the Development Case cites | Holds — `KubernetesConfig{ControlPlaneAddress}` and `StandaloneConfig{ResourcesPath}`, both in package config |
| `pkg/modes/modes.go` | the two first-class runtime modes | Holds — the whole package, as the earlier finding recorded |

Nine hold, none fails. That is why the two findings are narrow: this iteration's reading is well anchored where it was checked, and what fails is a cut that loses an area and two counts that do not reproduce.

#### Findings, at a glance

```plantuml
@startuml
title Findings of Survey iteration 3, technical lens - where each sits and who builds on it
hide empty members
skinparam classAttributeIconSize 0
skinparam wrapWidth 330
skinparam nodesep 28
skinparam ranksep 38

package "Iteration Plan (Project Management, Medium)" {
  class "F1 - Major, consumed-defect" as IPF1 {
    where : Objective O5; the cut tables; Coverage row for pkg/diagnostics
    defect : the area was re-cut into i3 to be read here
    defect : 34 of its 35 files are deferred to Excavation priority 4
    absent : no row of i4 or i5 carries pkg/diagnostics
    consequence : the 70-area sum counts it as reached here
    built on by : the SBC gate condition "no area unread"
    built on by : the Iteration Assessment that scores O5
    rework : yes
  }
}

package "System Inventory (Discovery, Critical)" {
  class "F5 - Minor, contained-defect" as SIF5 {
    where : Technology and Size, docs/development row and docker row
    defect : stated 13, enumerates 6+5+1 = 12, README omitted
    defect : stated 22, enumerates 8+1+5 = 14, and prose says 5 Windows against 8 named
    verdict : no role builds on either figure
    rework : owner's next turn, does not hold the milestone
  }
}

package "Closed this iteration - Reviewer lens" {
  class "Development Case#F1" as DCCL <<Resolved, was Minor>> {
    was : Deployment Model row cited pkg/config/modes/ for the two modes
    now : pkg/modes/modes.go for the modes, pkg/config/modes/ for what it holds
    verified : all three files opened, both halves true
  }
  class "System Inventory#F4" as SIF4 <<Resolved, was Major>> {
    was : 15 constants against 13 anchored registries, none opened
    now : plane stated from registry.go, manager.go and category.go
    verified : 13 fields and 12 dispatched categories counted in the files
  }
}

package "Not this lens" as O {
  class "other lenses" as OTHER {
    Management Reviewer : records its own findings at this review
    System Inventory#F1 (management) : an open Minor, not mine to close
    Status : not re-recorded here
  }
}

DCCL ..> IPF1 : the same test - a stated figure or placement against the tree
SIF4 ..> SIF5 : the same class F2 and F4 closed, a third place
IPF1 --> OTHER
SIF5 --> OTHER

note bottom of IPF1
  The distinction the finding turns on: reading one file of
  thirty-five and declaring the rest is the rule this phase
  follows. Declaring the rest to an order that no iteration of
  the cut carries is not, because the area is then counted as
  reached by the gate that asks whether any area is unread.
end note
@enduml
```

### Management Reviewer lens
<!-- review: Survey I3 -->

Two findings against the Iteration Plan: one Major re-recorded under its existing key, one Minor recorded this iteration. Nothing is recorded against the Risk List, the System Inventory or the Development Case from this lens — silence is approval, and the checks on each are in the compliance matrix.

#### Iteration Plan

**`Iteration Plan#F3` — Major — criterion `consumed-defect`.** The corrections this iteration applied to R017, R019 and R028 hold and are not at issue. What persists is the count those corrections were required to make true. Two of the twenty-seven placements name no area the iteration they sit on opens.

R013 (the version-skew patch corpus, `AN-007`) and R021 (the licence scan that reports and cannot fail, `AN-016`) both sit on iteration 4, supported by the sentence that their readings are "in `tests/` and `charts/`". Neither anomaly was read from either area: their anchors are `.github/scripts/version-skew-test-patches/` and `.github/workflows/fossa.yml`, both under `.github`, which this document assigns to iteration 1 and not to iteration 4's seventeen. R021's retirement is furthermore not a reading at all — its own mitigation in the Risk List makes it a Change Request against `.github/workflows/` owned by the Integrator, and the plan's own Resources section places the Integrator outside Survey, so no iteration of this cut confronts it.

The Evaluation Criteria record "27 are placed on an iteration and 5 are carried by the reading as a whole. A placement names an area the iteration it sits on opens", and Document Control repeats the figure. The count is again true as arithmetic and false as evidence, and the SBC verdict and Excavation's plan both read it as proof that the cut confronts every risk of the register. This is the defect recorded under this key in iteration 2, recurring in two rows that key's remediation did not name — and that remediation required the count itself to be truthful, not only the two rows it named.

*Remediation.* Either carry both past Survey — R013 retired where it was counted, in iteration 1's `.github` read, and R021 carried to Construction as the Integrator's Change Request, since no area of this cut restores a workflow gate — and restate the count as 25 placed, 5 carried by the reading as a whole and 2 carried past the phase; or, for each, name the area of iteration 4 that holds its retirement and state what that iteration reads of it. Then correct Document Control, the sequencing table's closing paragraph and the Evaluation Criteria's "Risks sequenced and accountable" row to the same figure, and take `.github` out of the i4 justification so that no row cites `tests/` or `charts/` as the place a `.github` fact is read.

**`Iteration Plan#F4` — Minor — criterion `contained-defect`.** Document Control states a milestone verdict for an iteration close and, in the same section, denies that the milestone is judged. It opens "The verdict on iteration 2 was SBC: iteration REQUIRED" and closes "The SBC milestone is not judged." The SBC gate is issued once, by the Review Coordinator, at the end of Survey when the cut holds no unread area; an iteration boundary is an Iteration Acceptance, and what it yields is a decision on the iteration, not a phase-gate verdict. Nothing else in the plan rests on the line, so the defect stays inside this document.

*Remediation.* Restate the opening sentence to what the iteration close actually decided — iteration 2 was reviewed and the phase iterates, with `Iteration Plan#F3` reworked in this plan's sequencing — and let the closing sentence carry the only statement about the milestone.

#### The placement, audited

```plantuml
@startuml
title The 27 placements of the cut, audited against the areas each iteration opens
hide empty members
skinparam classAttributeIconSize 0
skinparam wrapWidth 330
skinparam nodesep 24
skinparam ranksep 36

package "i1 - closed, 13 areas read" {
  class "11 risks" as I1 {
    High : R011, R016
    Significant : R009 R010 R012 R020 R022
    Moderate : R007 R008 R015 R018
    basis : each names an area i1 read
    verdict : holds
  }
}

package "i2 - closed, 15 of 16 read" {
  class "2 risks" as I2 {
    Significant : R017, R028
    basis : grpc/endpoints.go and http/server.go, both opened there
    verdict : holds - both restated this iteration
  }
}

package "i3 - this iteration, 12 areas" {
  class "9 risks" as I3 {
    High : R023
    Significant : R014 R019 R029 R030 R032
    Moderate : R024 R026 R031
    basis : pkg/scheduler, pkg/runtime, pkg/actors, pkg/apis, pkg/client, pkg/acl
    verdict : holds - every row names an area this slice opens
  }
}

package "i4 - 17 areas, none of them .github" {
  class "R001 R025 R027 - hold" as I4A {
    basis : tests/, charts/, pkg/testing - all three in i4's list
  }
  class "R013 R021 - do not hold" as I4B <<F3, Major>> {
    R013 : AN-007, .github/scripts/version-skew-test-patches/
    R021 : AN-016, .github/workflows/fossa.yml
    named : "read in tests/ and charts/" - neither area holds either file
    R021 also : a Change Request owned by the Integrator, a role Survey does not run
    consequence : 27 placed is arithmetic, not evidence
  }
}

package "no area of the cut - 5 risks" {
  class "R002 R003 R004 R005 R006" as W {
    basis : retired by the shape of every role's work order
    verdict : holds
  }
}

I1 --> I2
I2 --> I3
I3 --> I4A
I3 --> I4B
I4A --> W

note bottom of I4B
  The count the SBC verdict reads is 27 placed on an iteration.
  Two of the twenty-seven are confronted on paper only - the
  defect recorded under Iteration Plan#F3 in iteration 2, recurring
  in two rows that key's remediation did not name.
end note
@enduml
```

#### The Risk List

Nothing is recorded. I re-scored the register against the baseline scale and every magnitude is its exposure's band: R023 at 4 × 4 = 16 is High, R032 at 3 × 3 = 9 is Significant, R031 at 4 × 2 = 8 is Moderate. Each of the twenty-seven anomalies in the System Inventory carries exactly one refining risk, and the four this slice recorded are carried as R029 to R032 with `AN-024` to `AN-027` behind them. Custody is the Project Manager on all thirty-two and accountability is the role whose act retires the entry, so the separation last iteration's `Risk List#F1` demanded holds. No Significant or High risk is on `accept`, so no stakeholder grant is outstanding and none is asked.

R021's mitigation is the one entry whose accountable role does not work in this phase — the Integrator, for a Change Request against `.github/workflows/`. That is not a defect of the register: a risk retired in Construction is truthfully stated as retired there. The defect is the plan's, which places that risk on a Survey iteration that reads nothing of it, and it is recorded above under `Iteration Plan#F3`.

#### The System Inventory

Nothing is recorded. All twelve areas of the slice are assigned and every one is accounted for: eleven at a stated read depth, and `pkg/diagnostics` at one file of thirty-five, declared rather than skimmed. The counts reproduce — 2 + 2 + 9 + 2 + 1 + 1 + 2 + 4 + 1 + 0 + 2 + 1 = 27 in-slice, 30 distinct with the three outside, 118 for the phase — and the three outside-slice files each state the perimeter fact that could not be closed without them.

`pkg/diagnostics` is the area whose treatment I do not accept, but the finding is the Reviewer's and not mine. An area counted as reached while thirty-four of its thirty-five files leave the phase is a cut defect, and it is recorded under the Reviewer's `Iteration Plan#F1` against the plan that made the cut. Recording it a second time under this lens would put two rows on one defect.

```plantuml
@startuml
title pkg/diagnostics - where the area stands after each iteration of the cut
hide empty members
skinparam wrapWidth 300

[*] --> AssignedI2
state "Assigned to Survey i2" as AssignedI2 {
  AssignedI2 : 35 files, declared unread in full
  AssignedI2 : reason stated - a monitoring module is legible
  AssignedI2 : only against the operation it instruments
}

AssignedI2 --> ReCutI3 : the cut re-cuts it to i3, where that code is read
state "Survey i3 - this iteration" as ReCutI3 {
  ReCutI3 : 1 file opened - metrics.go, the seven default meters
  ReCutI3 : 34 unread, deferred to "priority 4"
  ReCutI3 : priority 4 is Excavation's reading order, not an iteration
  ReCutI3 : O5 not met on its own terms
}

ReCutI3 --> NoSlot
state "No iteration of the cut carries the remainder" as NoSlot {
  NoSlot : i4's 17 areas and i5's 13 do not list it
  NoSlot : the 70-area sum counts the area once, here
  NoSlot : the SBC condition "the cut holds no unread area"
  NoSlot : would be answered by 12 of 12 reached
}

NoSlot --> Declared : the plan states which of two outcomes it took
state "Declared - one of two, in the cut" as Declared {
  Declared : read the 13 modules against the operations W2-W4 read
  Declared : or carry the area to Excavation and drop it
  Declared : from this iteration's reached count
}

Declared --> [*]

note bottom of AssignedI2
  Recorded under the Reviewers lens as Iteration Plan F1, Major. It is not
  re-recorded under this lens: it is their key, and the closure is theirs.
  It enters this assessment because an area counted as reached while its
  substance leaves the phase is a scope fact, not only a reading fact.
end note
@enduml
```

#### The Development Case

Nothing is recorded. The three checks this lens owes the tailoring document hold: Business Modeling is INACTIVE on its stated trigger and the Business Reviewer therefore does not convene; the optional-trigger record stands with Glossary and Deployment Model FIRED and four NOT FIRED, each on its own condition; and the measurement policy — two quantities measured, both clocks apart, a human gate bounded as a risk and never forecast — is the rule against which I judged the plan and the register, and both follow it.

### Review Coordinator lens
<!-- review: Survey I2 -->

<!-- review: Survey I2 -->

The consolidated ledger is the authoritative finding list of this review. Each entry is copied from `read_artifact_findings` with its artifact, key, recording lens, severity and criterion; none is added, merged away or re-worded, and none is closed here — closure is the recording lens' own act.

#### Consolidated finding ledger

```plantuml
@startuml
title Consolidated finding ledger - Survey iteration 2, two lenses executed, four findings open
hide empty members
skinparam classAttributeIconSize 0
skinparam wrapWidth 340
skinparam nodesep 20
skinparam ranksep 44

package "Technical lens - Reviewer (EXECUTED)" {
  class "System Inventory#F4" as SIF4 <<Major, consumed-defect>> {
    where : Technology and Size pkg/components row;
            Components table; Coverage row
    defect : 15 Category constants, 13 anchored registry.go paths
    absent : pkg/components/workflow/registry.go
    absent : pkg/components/workflowbackend/registry.go
    also : 13 paths cited for files the row declares unread
    built on by : Excavation component recovery;
                  the Software Architect's component view
    rework : yes
  }
  class "Development Case#F1" as DCF1 <<Minor, contained-defect>> {
    where : Optional Artifact Triggers, Deployment Model row
    defect : cites pkg/config/modes/, a path the tree does not hold
    actual : pkg/modes/modes.go
    verdict : FIRED stands on the other evidence cited
    rework : owner's next turn
  }
}

package "Management lens - Management Reviewer (EXECUTED)" {
  class "Iteration Plan#F3" as IPF3 <<Major, consumed-defect>> {
    where : Risk-driven sequencing i2 row;
            Evaluation Criteria; measurement table
    defect : R017 and R019 placed on areas the same plan declares unread
    defect : R019's mitigation names pkg/scheduler/, carried by i3, which omits it
    built on by : the SBC verdict; Excavation's plan
    rework : yes
  }
  class "System Inventory#F1" as SIF1M <<Minor, contained-defect>> {
    where : Coverage, the sentence closing its introduction
    defect : twenty-four never-assigned areas against twenty-three rows
    built on by : nothing
    rework : owner's next turn
  }
}

package "Business lens - Business Reviewer (DID NOT EXECUTE)" {
  class "no evaluation" as BIZ <<INACTIVE>> {
    status : did not evaluate this review
    verdict : none - not Approved, not any other
    basis : Business Modeling INACTIVE, Development Case section 2
  }
}

package "Closed this review - six, each by its recording lens" {
  class "System Inventory#F1, F2, F3" as SICL <<Reviewer>> {
    F1 : actor-state claim re-anchored on Configuration.validate
    F2 : every Coverage row reproduces from its enumeration
    F3 : 42 / 19 / 16 and the proto breakdown derived from the tables
  }
  class "Iteration Plan#F1, F2" as IPCL <<Management Reviewer>> {
    F1 : the phase-length quotient deleted, the remainder stated as counts
    F2 : i1 reads 13 assigned and 13 reached; go.sum and img in i2's row only
  }
  class "Risk List#F1" as RLCL <<Management Reviewer>> {
    now : custody and accountability separate on all 28 rows
  }
}

class "Cross-lens conflict C1" as C1 <<resolved>> {
  subject : is O2 met - the component plane recorded
  management lens : O2 met, fifteen kinds anchored
  technical lens : the count does not hold, F4 Major
  resolved : the technical lens governs the evidentiary question
  outcome : O2 is met with rework - F4 stands, no finding created or closed
}

class "Key collision C2" as C2 <<resolved, no defect>> {
  subject : System Inventory#F1 names two findings
  Reviewer F1 : actor-state anchor, resolved this review
  Management F1 : the never-assigned count, open, Minor
  resolved : keys are scoped per artifact and per lens
  outcome : this ledger always cites the lens beside the key
}

SIF4 ..> C1 : the substance of it
IPF3 ..> C1 : the management lens' own pass
SIF1M ..> C2 : the second F1 on this artifact
SICL ..> C2 : the first F1 on this artifact
SIF4 --> BIZ
DCF1 --> BIZ
IPF3 --> BIZ
SIF1M --> BIZ

note bottom of C1
  The management lens passed O2 on the artifact's own statement; the
  technical lens opened the tree. Where they differ on what an anchor
  holds, the opened file decides. No lens withdraws a finding here.
end note
note bottom of C2
  The finding key is scoped per artifact and per reviewer lens. A ledger
  that cites "System Inventory#F1" without the lens would record a closed
  Major and an open Minor as the same entry.
end note
@enduml
```

| Finding | Severity | Criterion | Artifact | Recording lens | Built on by | Rework |
|---|---|---|---|---|---|---|
| `System Inventory#F4` | Major | `consumed-defect` | System Inventory | Reviewer | Excavation's component recovery; the Software Architect's component view; O2's exit criterion | Yes |
| `Iteration Plan#F3` | Major | `consumed-defect` | Iteration Plan | Management Reviewer | The SBC verdict; Excavation's plan, which reads the placement count | Yes |
| `System Inventory#F1` | Minor | `contained-defect` | System Inventory | Management Reviewer | Nothing | Owner's next turn |
| `Development Case#F1` | Minor | `contained-defect` | Development Case | Reviewer | Nothing | Owner's next turn |

Two Major, two Minor, zero Critical, zero Info. No finding is open against the Risk List: its one finding from iteration 1 is closed this review, and the register's scale, arithmetic, anomaly coverage and accountability were re-checked by both lenses and hold.

#### The two Major findings, and why they hold the milestone

`System Inventory#F4` is the only finding of this review that changes what another role may build on. `pkg/components/category.go` declares fifteen `Category` constants and the artifact derives "one registry per category" from that file, anchoring thirteen `registry.go` paths — two of the fifteen, `workflow` and `workflowbackend`, resolve to nothing in the tree. The thirteen that do exist were opened for this review and all follow the pattern the row describes, so the row is true for thirteen and silent about the two the workflow engine and its actor backend depend on. The same row declares fifty-two of the area's fifty-three files unread, so every one of the thirteen cited paths is an anchor for a file the artifact states it never opened. Excavation's component recovery and the Software Architect's component view both build on the registry set being complete; O2 of this iteration's plan asks for that set by name.

`Iteration Plan#F3` places two of the twenty-eight risks on iterations whose reading the same plan excludes. R017 is placed on this iteration because the allowlist's operations are "implemented in `pkg/api/universal/`", which W4 of the same plan states is not part of the read; R019 is placed on this iteration against `pkg/components/`, which holds nothing about the scheduler's failure-policy families, while its own mitigation names `pkg/scheduler/` — an area iteration 3 reads and whose row omits it. The Evaluation Criteria and the measurement table then read "28 risks, all placed" as evidence that the cut confronts every risk of the register. The count is true as arithmetic and false as evidence, and the SBC verdict and Excavation's plan both read it.

Neither Major stops this iteration's acceptance. A Major is reworked before any role consumes the failing content again, and the milestone is not sanctioned while it stands; the phase iterates either way.

#### Cross-lens conflicts, resolved

| # | Conflict | Resolution | Where it lands |
|---|---|---|---|
| C1 | The management lens passed O2 — "the component plane recorded, fifteen kinds anchored" — while the technical lens recorded the registry count failing as a Major. | Not a disagreement to arbitrate between lenses: it is a question of fact, and an anchor opened against the tree settles it. The opened file governs, so the technical lens' F4 stands and O2 is read as met with rework. Neither lens withdraws a finding; no finding is created or closed by this resolution. | `System Inventory#F4` open; the plan's O2 outcome restated in the Disposition |
| C2 | Two open-and-closed findings carry the key `System Inventory#F1` — the Reviewer's actor-state Major, resolved this review, and the Management Reviewer's never-assigned-count Minor, open. | Not a conflict but a key collision, and it is correct behaviour: a finding key is scoped per artifact and per reviewer lens. The resolution is in this ledger's form — every citation names the recording lens beside the key, so a closed Major and an open Minor never merge into one row. | No artifact changes; the ledger cites the lens throughout |

No conflict required a lens to withdraw a finding. Neither lens claimed a finding the other had recorded, which is the failure mode a consolidation looks for first: the management lens explicitly declined to re-record F4 under its own key and the technical lens explicitly declined to close the management lens' findings.

#### What no lens recorded

| Check | Both executing lenses |
|---|---|
| Critical finding | None. Both recorded zero, so nothing escalates to the stakeholder and no decision is asked of them |
| Finding against an artifact outside the four reviewed | None. Four artifacts exist for this phase iteration and all four were read; the Iteration Assessment was read and not assessed as evidence, which is not a defect in it |
| Pull request to dispose of | None in any state. Survey produces no code and the Implementation Model does not exist yet |
| Invented figure | None found in any of the four artifacts — no token spend, elapsed time, work-item size, velocity, density or projected date. The one measured actual the phase holds is iteration 1's, quoted as that iteration's own fact with both clocks apart |
| Finding carried as a new key that belongs to an earlier one | None. All four open findings are new defects; the six earlier ones were closed under their existing keys and the technical lens states why F4 is not a re-record of F2 and F3 — those closed their own rows and did not reach this one |
| Recovered element carrying an identifier family it does not own | None. Decision records are cited as evidence and not renumbered; the three `DependsOn` edges match the trace graph exactly |

## Resolutions and Actions
### Reviewer lens
<!-- review: Survey I3 -->

<!-- review: Survey I3 -->

Two findings of this lens from earlier iterations are closed this turn, each on evidence read from the corrected artifact and from the file it cites rather than on the owner's word.

#### Findings closed this review

| Finding | Severity | How the defect was fixed | Evidence I verified |
|---|---|---|---|
| `Development Case#F1` | Minor | The Deployment Model row no longer cites `pkg/config/modes/` as the package where the two runtime modes are declared. It anchors the two first-class modes on `pkg/modes/modes.go` and cites `pkg/config/modes/` separately for what it does hold: two per-mode configuration files. The paragraph introducing the section now states that every path in the Evidence column resolves and every count beside one is a count of what that path holds. | I opened all three files. `pkg/config/modes/kubernetes_config.go` declares `KubernetesConfig{ControlPlaneAddress string}` and `standalone_config.go` declares `StandaloneConfig{ResourcesPath []string}`, both in package config — so the second half of the citation is true as well as the first. Spot-checked figures: Sentry 36, placement 26, scheduler 76, subcharts 7, Dockerfiles 8 — all hold. |
| `System Inventory#F4` | Major | The component plane is stated from the files that hold it, in every place the finding named. Thirteen registry paths are no longer presented as anchors for unopened files; the plane anchors `category.go`, `registry.go` and the `pkg/components/` directory listing, all three open. The two categories reachable by neither a registry nor a shim are stated as such in AN-024 with the code path that reaches the actor plane without them. | I opened `pkg/runtime/registry/registry.go`: exactly thirteen registry fields plus `componentCb` and `reporter`, and no workflow field. I opened `pkg/runtime/processor/manager.go`: `category()` lists twelve `components.Category` values, which is the thirteen less `nameresolution`. The row's arithmetic now reproduces from the code. The consuming end is closed in turn — the Project Manager scored R029 on AN-024. |

`System Inventory#F5`, recorded this iteration, is the same class of defect as `#F2` and `#F4` — a stated count that does not reconcile with what the row itself prints — appearing in two rows the earlier corrections did not reach. That is the reason it is recorded rather than folded into either: closing F2 and F4 closed those rows, and did not close these.

#### Actions this review sets

| # | Action | Owner | Closes | Holds the SBC gate |
|---|---|---|---|---|
| 1 | Read the thirteen `pkg/diagnostics` modules against the operations W2–W4 recovered — the scheduler's eight operations, the placement leader gate, the Sentry validators, the actor table's suspension, the internal server's five handlers — stating each against the operation it instruments and declaring the ones that instrument nothing this phase read; or state in the cut that the area is carried to Excavation and drop it from this iteration's reached count | Software Archaeologist for the read; Project Manager for the cut | `Iteration Plan#F1` | Yes — the SBC condition "the cut holds no unread area" is answered by this count |
| 2 | Name `docs/development/README.md` among the files of the `docs/development` row; reconcile the `docker` row's Dockerfile count and account for the remaining files of its 22; then re-check the rest of Technology and Size the same way | Software Archaeologist | `System Inventory#F5` | No — owner's next turn |

Action 1 is one act worked as a whole: the Coverage row, the plan's cut tables and the reached count must move together, or a reader is left with an area declared reached by a count that no longer matches its reading.

#### What this lens does not close

`System Inventory#F1` — the Management Reviewer's Minor on the never-assigned count in Coverage — is open under that lens and is not mine to close. I read the Coverage table it names and observe that the introduction now derives its statement from the rows at the foot of the table rather than asserting a number, which is what that finding's remediation asked for. That observation is recorded; the closure is the recording lens' act.

The Risk List carries no open finding from any lens and nothing is recorded against it here.

#### Pull requests

`scm_list_pull_requests` returns no pull request in any state, so there is nothing to approve, request changes on, or defer. That is correct for Survey: no role of this phase produces code, and the Implementation Model does not exist yet.

#### Defect distribution

```plantuml
@startuml
title Defect distribution - Survey iteration 3, technical lens
hide empty members
skinparam classAttributeIconSize 0
skinparam wrapWidth 300
skinparam nodesep 30
skinparam ranksep 40

class "Iteration Plan" as IP {
  Critical : 0
  Major : 1  (F1)
  Minor : 0
  Info : 0
}

class "System Inventory" as SI {
  Critical : 0
  Major : 0
  Minor : 1  (F5)
  Info : 0
  closed this review : F4 - Resolved
}

class "Development Case" as DC {
  Critical : 0
  Major : 0
  Minor : 0
  Info : 0
  closed this review : F1 - Resolved
}

class "Risk List" as RL {
  Critical : 0
  Major : 0
  Minor : 0
  Info : 0
  note : approved from this lens - nothing recorded
}

class "This review, in totals" as TOT <<current, not a rate>> {
  findings recorded : 2
  Major : 1
  Minor : 1
  Critical : 0
  Info : 0
  findings closed : 2
  net open against this lens : 2
}

IP --> TOT
SI --> TOT
DC --> TOT
RL --> TOT

note bottom of TOT
  Counts per artifact, never per KLOC: Survey holds no size
  measure to normalise by. Two closed and two opened is not a
  trend - the same measure was not taken twice on the same
  work, and the phase has no per-iteration rate.
end note
@enduml
```

### Management Reviewer lens
<!-- review: Survey I3 -->

One finding of this lens is closed this turn and one persists under its key. `System Inventory#F1` is closed on evidence read from the corrected artifact; `Iteration Plan#F3` is re-recorded because two of its placements still name no area.

#### Findings closed this review

| Finding | Severity | How the defect was fixed | Evidence I verified |
|---|---|---|---|
| `System Inventory#F1` | Minor | The sentence closing the Coverage introduction no longer asserts a number. It now reads "The areas at the foot of this table, none of them assigned to a slice, are declared unread there" — the second outcome the remediation admitted, in which the statement cannot drift from the rows when a later slice leaves one fewer. | I counted the never-assigned rows myself: twenty-three, `docker` through `utils`, against the twenty-four the earlier sentence claimed. The three counts beside the sentence still reproduce: iteration 3's per-area reads 2 + 2 + 9 + 2 + 1 + 1 + 2 + 4 + 1 + 0 + 2 + 1 = 27, its 30 distinct with the three outside the slice, and 60 + 28 + 30 = 118 for the phase. |

#### Findings re-recorded under their key

| Finding | Severity | Why it stays open | What closes it |
|---|---|---|---|
| `Iteration Plan#F3` | Major — `consumed-defect` | The three rows its remediation named are corrected: R017 now rests on the allowlist map with no claim about `pkg/api/universal/`, R019 sits on this iteration beside `pkg/scheduler/`, and R028 on `pkg/api/http/server.go`. The count those corrections were required to make true is not. R013 and R021 remain on iteration 4, supported by "read in `tests/` and `charts/`", and neither anomaly was read from either area — both anchor under `.github`, an area of iteration 1. R021's retirement is a Change Request owned by the Integrator, a role this phase does not run. | The count stated in Document Control, in the sequencing table's closing paragraph and in the Evaluation Criteria's "Risks sequenced and accountable" row moves to 25 placed, 5 whole-reading and 2 carried past the phase; or each of the two names the area of iteration 4 that reads its retirement. |

#### Findings recorded this review

| Finding | Severity | Criterion | Location | Rework |
|---|---|---|---|---|
| `Iteration Plan#F4` | Minor | `contained-defect` | Document Control — "The verdict on iteration 2 was SBC: iteration REQUIRED" against "The SBC milestone is not judged" | Owner's next turn; holds no gate |

#### Actions this review sets

| # | Action | Owner | Closes | Holds the SBC gate |
|---|---|---|---|---|
| 1 | State R013 and R021 at what the cut actually does with them, or name the area of iteration 4 that reads each; then move Document Control, the sequencing table's closing paragraph and the Evaluation Criteria's "Risks sequenced and accountable" row to the same figure, and take `.github` out of the i4 justification | Project Manager | `Iteration Plan#F3` | Yes — the SBC verdict reads the placed count as evidence that the cut confronts every risk of the register |
| 2 | Restate Document Control's opening sentence to the decision the iteration close produced, leaving the milestone's status to the closing sentence | Project Manager | `Iteration Plan#F4` | No |

Action 1 spans four places that quote one figure — Document Control, the sequencing table, its closing paragraph and the Evaluation Criteria row — and they are written together. A corrected count in one and the old figure in another reproduces the defect the finding records.

Neither action is assigned to a role that does not work in this phase. Neither carries a size, an effort figure or a calendar date: none has been measured, and a date projected from an estimate would read downstream as an observation.

#### What this lens does not close

The Reviewer's `Iteration Plan#F1` — `pkg/diagnostics` re-cut into this iteration and thirty-four of its thirty-five files deferred to an order no iteration of the cut runs — is open under that lens and is not mine to close. It is a scope fact this assessment records rather than a second finding: an area counted as reached while its substance leaves the phase understates what the phase has left to read, and the SBC condition "the cut holds no unread area" would be answered by a count that is short thirty-four files. I read the Coverage row and the two cut tables it names and observe the deferral is real; the disposition below treats the area as carried, not accepted.

### Review Coordinator lens
<!-- review: Survey I2 -->

<!-- review: Survey I2 -->

Six findings were closed this review, each by the lens that recorded it, each on evidence read from the corrected content rather than from a note saying it was done. Four remain open. No closure is performed or claimed in this section: the closure column is an obligation assigned to the recording lens.

#### Finding lifecycle at this gate

```plantuml
@startuml
title Finding lifecycle and the prioritized action list at the close of Survey iteration 2
skinparam wrapWidth 250

state "Open - 4 findings at this close" as Open {
  state "Major - 2, these hold the SBC gate" as Major {
    SI_F4 : System Inventory#F4, Reviewer, the registry set
    IP_F3 : Iteration Plan#F3, Management Reviewer, risk placement
  }
  state "Minor - 2, owner's next turn, gate not held" as Minor {
    SI_F1M : System Inventory#F1, Management Reviewer, twenty-four against twenty-three
    DC_F1 : Development Case#F1, Reviewer, a path the tree lacks
  }
}

[*] --> Open : recorded by its lens this review
Open --> Assigned : owner and action named below
Assigned --> Reworked : the owner corrects the artifact
Reworked --> Closed : resolve_artifact_finding by the SAME lens that recorded it
Reworked --> Open : the defect still stands, re-recorded under its key
Closed --> [*]

state "Closed this review - 6" as Closed {
  SI_F1 : System Inventory#F1, Reviewer - actor-state anchor
  SI_F2 : System Inventory#F2, Reviewer - read-counts
  SI_F3 : System Inventory#F3, Reviewer - stated counts
  IP_F1 : Iteration Plan#F1, Management Reviewer - the quotient
  IP_F2 : Iteration Plan#F2, Management Reviewer - the i1 row
  RL_F1 : Risk List#F1, Management Reviewer - accountability
}

state "Order of work - by what blocks what" as Order {
  A1 : 1 System Inventory#F4 - Software Archaeologist
  A2 : 2 Iteration Plan#F3 - Project Manager
  A3 : 3 System Inventory#F4 consuming end - risk for two unlocated categories
  A4 : 4 System Inventory#F1 - Software Archaeologist
  A5 : 5 Development Case#F1 - Process Engineer
}

Order --> Assigned : each closure verified against the corrected content

note bottom of Major
  A Major is reworked before any role consumes the failing content again,
  and the milestone is not sanctioned while it stands. It does not stop
  this iteration's acceptance: the phase iterates either way.
end note
note bottom of Order
  A1 before A3 because the count must be true before a risk can be scored
  against it. A4 and A5 follow the two Major, and neither holds the gate.
  No action carries a size, an effort figure or a projected date.
end note
@enduml
```

#### Prioritized actions

Priority is set by what blocks what, not by severity alone. A1 comes first because O2 of the plan asks for the registry set by name and every component artifact of Excavation builds on it; A2 second because the SBC verdict reads the risk placement as evidence that the cut confronts every risk of the register.

| # | Action | Owner | Closes | Deadline | Holds the SBC gate |
|---|---|---|---|---|---|
| 1 | For each of the fifteen category constants name the file that registers it; locate the registration of `workflow` and `workflowbackend` or state that it is not located; move the unopened `registry.go` paths out of the anchor column into a not-yet-opened column, or open them; restate the Components table row to match | Software Archaeologist | `System Inventory#F4` | Before Excavation's component recovery consumes the registry set | Yes |
| 2 | Restate the risk-driven sequencing row to the risks this slice's reading actually confronts: R028 on `pkg/api/http/server.go`; R017 only on the reading that records the allowlist map as the access-control surface, with no claim about `universal/`; move R019 to the i3 row beside R023, R014, R024 and R026, where `pkg/scheduler/` is read; then keep the placed count truthful, each placement naming an area that iteration opens | Project Manager | `Iteration Plan#F3` | Before the SBC gate is asked | Yes |
| 3 | Carry the two unlocated categories as a risk, scored on the register's own scale, naming the accountable role whose reading locates them | Project Manager | `System Inventory#F4` at its consuming end | Immediately after action 1 states the count | Yes |
| 4 | State the never-assigned count as twenty-three, or derive it from the rows at the foot of the Coverage table so it cannot drift | Software Archaeologist | `System Inventory#F1` (Management Reviewer) | Owner's next turn | No |
| 5 | Correct the Deployment Model evidence path to `pkg/modes/modes.go` and cite the file; check the other rows of the same table for paths that do not resolve | Process Engineer | `Development Case#F1` | Owner's next turn | No |

Action 1 is one act of correction worked as a whole: a row naming thirteen of fifteen categories and anchoring thirteen paths must be fixed in the count and in the anchors together, or the reader is left with a corrected number over uncorrected evidence. Action 2 spans one row and three other places that quote it — the Evaluation Criteria, the measurement table's unplaced-risk count, and the plan's Objective O5 statement — and all four are written together.

Actions 4 and 5 are Minor and do not hold the gate. They are ordered after the two Major because the severity contract runs Critical, then Major, then Minor, and not because they are unimportant: each is a single stated figure or path that does not hold, and each is inside its own artifact.

No action is assigned to a role that does not work in this phase. No action carries a size, an effort figure or a calendar date — none has been measured, and a date projected from an estimate would read downstream as an observation.

#### Which lens closes what

| Finding | Severity | Closed only by | Verification the lens applies |
|---|---|---|---|
| `System Inventory#F4` | Major | Reviewer | Each of the fifteen constants is matched to a file that was read; the two unlocated categories are stated as unlocated or their registration is cited; no anchor names a file the Coverage table declares unread |
| `Iteration Plan#F3` | Major | Management Reviewer | Every placement names an area the named iteration opens; the placed count is true on that reading; a risk carried past Survey is stated as carried, not placed on an unopened area |
| `System Inventory#F1` | Minor | Management Reviewer | The count equals the never-assigned rows, or is derived from them |
| `Development Case#F1` | Minor | Reviewer | The cited path resolves in the tree and the file declares what the row claims |

A closure by a lens other than the recorder is rejected. A defect that persists into the next iteration keeps its key and is re-recorded under it, never as a new finding, so review debt stays countable.

#### What this review does not add to the increment

Two optional artifacts are FIRED by the Development Case — the Glossary and the Deployment Model — and both are produced in later phases. Neither is due in Survey, neither was reviewed here, and nothing is recorded against them. The remaining four optionals are NOT FIRED and none is produced.

No action asks any role to re-read an area. Actions 1, 2, 4 and 5 are corrections to artifacts' accounts of what was already read; action 3 is a register entry scored against a count once that count is true. The cut's next slice — the eleven areas of Survey i3 — is set by the plan and is unchanged by this review.

## Disposition
### Reviewer lens
<!-- review: Survey I3 -->

<!-- review: Survey I3 -->

**Approved with Changes.** No Critical finding: nothing in this iteration needs a decision only the stakeholder can make, and no `REQUIRES_USER_INPUT` is raised.

One Major finding (`Iteration Plan#F1`) and one Minor (`System Inventory#F5`). Under the severity contract the artifact is reworked before any role consumes the failing content again, though the plan does not hold this iteration's acceptance: a Major holds the SBC milestone, and the phase iterates either way. Two prior findings of this lens — `Development Case#F1` and `System Inventory#F4` — are closed, each verified against the files its correction cites.

The iteration met its reading. Twelve of twelve assigned areas were accounted for, eleven of them read to a stated depth with the remainder declared into the cut. Thirty distinct files were opened — twenty-seven in-slice and three outside it with the reason stated for each — bringing the phase to 118 of the tree's 6 631 entries. All nine anchors I opened independently say what the artifacts claim. That proportion is why the findings are narrow: this reading is well anchored where it was checked, and what fails is a cut that loses an area and two counts that do not reproduce.

This disposition is the technical lens' judgment of the iteration's completion. It is not the SBC verdict — that is the Review Coordinator's, taken after the Management Reviewer.

#### What this lens hands to the next two

| Recipient | What it inherits | What it must not do |
|---|---|---|
| Management Reviewer | `Iteration Plan#F1` and `System Inventory#F5` open from this lens; its own `System Inventory#F1` still open. I read the Coverage introduction and observed that it now derives its statement from the rows rather than asserting a number — recorded, not closed, because closure is that lens' act | Re-record F1 or F5 under its own key. Both are technical defects and this lens owns them |
| Review Coordinator | Zero open Critical from this lens; one open Major from this lens; its own `System Inventory#F1` if it remains open. The SBC gate is not sanctioned while a Major stands | Issue the SBC verdict on this review. This is an Iteration Acceptance and the milestone's exit criteria are not judged here |

The Minor finding does not hold the gate. Under the severity contract its owner fixes it in its next turn, after the Critical and Major work.

```plantuml
@startuml
title Survey iteration 3, technical lens - the disposition and what it hands on
hide empty members
skinparam classAttributeIconSize 0
skinparam wrapWidth 330
skinparam nodesep 30
skinparam ranksep 42

package "What this lens found" {
  class "Reconciled" as REC <<2 of 2 closed>> {
    Development Case#F1 : the cited path now resolves
    System Inventory#F4 : the plane stated from the files
    verified : each against files opened for this review
  }
  class "New - Major, 1" as NEWM <<holds the gate>> {
    Iteration Plan#F1 : pkg/diagnostics re-cut here, then deferred out of the cut
    built on by : the SBC condition that no area is unread
  }
  class "New - Minor, 1" as NEWN <<owner's next turn>> {
    System Inventory#F5 : two rows whose counts their enumeration does not yield
  }
  class "Critical, 0" as CRIT <<nothing escalates>> {
    stakeholder : asked nothing at an Iteration Acceptance
    REQUIRES_USER_INPUT : not raised
  }
}

package "Disposition" {
  class "Approved with Changes" as VERD <<technical lens>> {
    basis : 12 of 12 areas accounted for at a stated depth
    basis : 9 anchors opened independently, 9 held
    basis : 27 in-slice + 3 outside = 30; 118 for the phase, all reproducing
    condition : F1 worked before the SBC gate reads the reached count
    condition : F5 fixed in the owner's next turn
    not the SBC verdict : the Review Coordinator's
  }
}

package "What this lens hands on" {
  class "To the Management Reviewer" as NEXT <<next lens>> {
    open : Iteration Plan#F1 from this lens, Major
    open : System Inventory#F5 from this lens, Minor
    own : System Inventory#F1 under its own key, untouched here
  }
  class "To the Review Coordinator" as COORD <<the verdict>> {
    open Critical : 0 - nothing escalates
    open Major : Iteration Plan#F1 from this lens
    open Minor : System Inventory#F5 from this lens
    gate : not sanctioned while a Major stands
  }
}

REC --> VERD
NEWM --> VERD
NEWN --> VERD
CRIT --> VERD
VERD --> NEXT
VERD --> COORD

note bottom of NEWM
  Narrow and self-contained. Every other area of the slice is
  read to a stated depth and its remainder declared into the
  cut; this one area's remainder has no iteration to land in.
end note
note bottom of CRIT
  An Iteration Acceptance asks the stakeholder nothing. Only
  an unresolved Critical finding goes to them, and this lens
  recorded none this iteration or the two before it.
end note
@enduml
```

### Management Reviewer lens
<!-- review: Survey I3 -->

**Verdict on Survey iteration 3, management lens: Conditional Go on the iteration.** The eleven areas of the slice other than `pkg/diagnostics` are accepted at the depth Coverage declares; `pkg/diagnostics` is carried, not accepted, and the iteration's reached count of twelve is not confirmed by this lens. Two conditions attach, one of which holds the SBC gate.

The System Boundary Confirmed milestone is **not judged**. No milestone, iteration or phase is recorded as complete by this review.

| Axis | State | What it rests on |
|---|---|---|
| Scope | Holds, with one area carried | Twelve areas assigned in the Work Order's order, eleven read to a stated depth. `pkg/diagnostics` opened one file of thirty-five and deferred the rest to a reading order no Survey iteration runs — carried, not accepted |
| Schedule | Holds, and is unmeasured | Thirty areas remain across the two iterations that follow, stated as counts of areas. No phase has closed, nothing is projected, and `R001` stands as the overrun risk with one more iteration as the remedy and never less scope |
| Cost | Reported, never forecast | Iteration 1 at 19 480 197 tokens and 0:36:59.0766494 of agent time, iteration 2 at 30 715 328 tokens and 0:48:19.7330924, each gate's queue time measured apart at 0:00:00. Two actuals are not a velocity; no rate and no forecast is derived from them, and the plan states none |
| Quality | Holds, with rework outstanding | Four anomalies recorded in this slice, each carried as a scored risk, and `R023` — the third High risk — read from the code rather than from configuration. Two Major findings stand on the plan's account of itself: the Reviewer's `Iteration Plan#F1` on the carried area and this lens' `Iteration Plan#F3` on the placement count |

```plantuml
@startuml
title Management assessment of Survey iteration 3 - four axes, and what the verdict rests on
hide empty members
skinparam classAttributeIconSize 0
skinparam wrapWidth 320
skinparam nodesep 26
skinparam ranksep 36

class "Scope" as SCOPE {
  state : holds
  evidence : 12 areas assigned, 11 read, pkg/diagnostics at 1 of 35
  evidence : no area added to or dropped from the Work Order's order
  open : the diagnostics remainder has no iteration to land in - Reviewers lens, Iteration Plan F1
}

class "Schedule" as SCHED {
  state : holds, and is unmeasured
  evidence : 30 areas across i4 and i5, stated as counts, nothing projected
  evidence : no phase has closed, so no phase length is forecast
  open : R001 is the overrun risk; the remedy is one more iteration, never less scope
}

class "Cost" as COST {
  state : reported, never forecast
  evidence : i1 19 480 197 tokens and 0:36:59.0766494 agent time
  evidence : i2 30 715 328 tokens and 0:48:19.7330924 agent time
  evidence : both gates measured apart at 0:00:00 of queue time
  open : two actuals are not a velocity and nothing is projected from them
}

class "Quality" as QUAL {
  state : holds with rework outstanding
  evidence : 4 anomalies recorded this slice, each carried as a scored risk
  evidence : R023 is the third High risk, uncovered by this reading and avoided
  open : two Major findings on the plan's account of itself - F3 here, Reviewer F1
}

class "Verdict" as V {
  iteration : conditional - 11 of 12 areas accepted, pkg/diagnostics carried
  sbc : not judged
  critical : zero, so nothing escalates to the stakeholder
  escalation : none asked and none outstanding
}

SCOPE --> V
SCHED --> V
COST --> V
QUAL --> V
@enduml
```

**The two conditions.**

1. **`Iteration Plan#F3` — holds the SBC gate.** The placed count must be true on the reading the plan declares: 25 placed on an iteration, 5 carried by the reading as a whole, 2 carried past the phase; or each of R013 and R021 must name the area of iteration 4 that reads its retirement. Until it moves, the SBC verdict would be answered by a count two of whose twenty-seven placements are paper.
2. **`Iteration Plan#F4` — holds no gate.** Document Control's opening sentence states an SBC verdict for an iteration close and the same section denies the milestone is judged. The owner corrects it in its next turn.

**What this lens found no defect in.** The Risk List: every magnitude is its exposure's band on the baseline scale, all twenty-seven anomalies are carried by a refining risk, custody is separated from accountability on every row, and no Significant or High risk is on accept with a grant outstanding. The Development Case: the tailoring contradicts no part of the inherited process — nine disciplines active at the canonical matrix's intensity with no deviation claimed, Business Modeling the one INACTIVE delta on its stated trigger, the roster intact with no role merged or reassigned, and two optionals FIRED and four NOT FIRED each against its own §5.2 condition. Its measurement policy is the rule the plan and the register were judged against, and both follow it.

**Escalation.** Zero Critical findings. Nothing in this review needs a decision only the stakeholder can take, so no question is asked of them and none is outstanding. Both findings are the plan's owner's to correct.

**On the phase.** The SBC gate opens when the cut holds no unread area and every Major finding of the phase has its rework in the baseline. It does not open on this review's reading: two Major findings stand open, the Reviewer's `Iteration Plan#F1` and this lens' `Iteration Plan#F3`, and one area, `pkg/diagnostics`, has a declared remainder with no iteration of the cut to land in. The phase iterates; it does not advance here.

### Review Coordinator lens
<!-- review: Survey I2 -->

<!-- review: Survey I2 -->

**Iteration Accepted — the phase iterates. The SBC milestone is not judged and no phase is closed.**

No Critical finding is open. Both executing lenses recorded zero, so nothing escalates to the stakeholder, `REQUIRES_USER_INPUT` is not raised, and this record asks them nothing. An Iteration Acceptance seeks no phase sanction; only an unresolved Critical finding goes to them.

#### Lenses convened, and the one that did not evaluate

| Lens | Participant | Outcome |
|---|---|---|
| Technical | Reviewer | EXECUTED — 2 findings recorded (1 Major, 1 Minor), 3 closed, disposition Approved with Changes |
| Management | Management Reviewer | EXECUTED — 2 findings recorded (1 Major, 1 Minor), 3 closed, disposition Conditional Go on the iteration |
| Business | Business Reviewer | **INACTIVE — did not evaluate this review** |

The Business Reviewer recorded no verdict of any kind and none is attributed to it. It does not work in this project: Business Modeling is INACTIVE in every phase by the Development Case, so the role is withheld by the tailoring rather than absent from this event. The decision below rests only on the two lenses that executed.

#### Review effectiveness — current metrics, and the one comparison two events allow

```plantuml
@startuml
title Review effectiveness - Survey iteration 2, with the one comparison a second review event makes possible
hide empty members
skinparam classAttributeIconSize 0
skinparam wrapWidth 330
skinparam ranksep 40
skinparam nodesep 22

package "Coverage of this iteration's increment" {
  class "System Inventory" as SI <<Discovery, Critical>> {
    reviewed : yes, both executing lenses
    recorded : 1 Major (F4) - F1, F2, F3 closed here
  }
  class "Development Case" as DC <<Environment, High>> {
    reviewed : yes, both executing lenses
    recorded : 1 Minor (F1), no new Major
  }
  class "Iteration Plan" as IP <<Project Management, Medium>> {
    reviewed : yes, both executing lenses
    recorded : 1 Major (F3) - F1, F2 closed here
  }
  class "Risk List" as RL <<Project Management, Medium>> {
    reviewed : yes, both executing lenses
    recorded : 0 - F1 closed here
  }
  class "Iteration Assessment" as IA <<Project Management>> {
    reviewed : read, not assessed as evidence
    reason : it records iteration 1 and says so; the PM writes i2 after this verdict
  }
}

class "Assigned slice" as SLICE <<scope of record>> {
  areas assigned : 16, in the order the Work Order names
  areas reached : 15
  declared not reached : pkg/diagnostics - unread in full, reason stated
  files opened : 28 distinct this iteration; 88 across the phase
}

class "Findings by severity" as SEV <<this review>> {
  Critical : 0
  Major : 2
  Minor : 2
  Info : 0
  recorded : 4
  closed : 6
  net open at close : 4
}

class "Two review events compared" as TREND <<i1 and i2, both occurred>> {
  review events in the phase : 2
  findings recorded : 6 then 4
  Major recorded : 4 then 2
  Critical recorded : 0 then 0
  closed by the later review : 6 of the 6 the earlier left open
  reading : not a rate, not a velocity, no forecast
}

class "Anchor audit - technical lens" as AUDIT <<sampled>> {
  i1 : 12 re-opened, 11 held, 1 failed
  i2 : 13 re-opened, 10 held, 3 failed
  basis : the Reviewer opened each file rather than trusting the artifact
}

class "Not measurable at this gate" as NM <<no figure invented>> {
  defect density per KLOC : Survey holds no size measure
  defect removal efficiency : no Test discipline runs in Survey
  rework effort : no token or elapsed reading taken at the gate
}

SLICE --> SEV
SI --> SEV
DC --> SEV
IP --> SEV
RL --> SEV
IA --> SEV
SEV --> TREND
AUDIT --> TREND
SEV --> NM

note bottom of TREND
  Two events occurred, so they are comparable - they are the only two
  readings this phase holds. Fewer findings at the later event is not
  evidence of a better process: the slices differ in size and in what
  they made legible, and no per-iteration figure is a velocity.
end note
note bottom of AUDIT
  Six of twenty-five anchors re-opened across the two reviews did not
  hold. All three failures of this event sit in two rows of two artifacts
  and in a cited path - narrow defects in an otherwise verified reading.
end note
@enduml
```

| Effectiveness indicator | Value at this review | What it means here |
|---|---|---|
| Review coverage of planned artifacts | 4 of 4 — 100 percent | Every artifact the Development Case sanctions for this iteration was reviewed by both executing lenses |
| Review coverage of the assigned slice | 15 of 16 reached; 1 declared unread with its reason | No area of the assignment went unjudged or silently dropped: `pkg/diagnostics` is declared, and the next iteration's cut carries it |
| Findings recorded | 4 | 2 Major, 2 Minor, 0 Critical, 0 Info |
| Findings closed | 6 | All six that earlier reviews left open, each by its recording lens, each verified against the corrected content |
| Findings per artifact | System Inventory 2, Iteration Plan 1, Development Case 1, Risk List 0 | A bare count, not a density: the artifacts differ in type and size and Survey holds no measure to normalise by |
| Defect density per size | Not measurable | Survey holds no size measure. A per-KLOC figure would be `[ASSUMPTION — requires validation]` and is not stated |
| Defect removal efficiency | Not measurable | No Test discipline runs in Survey, so there is no test-found population to compare review-found defects against. It becomes measurable in Construction |
| Rework effort | Not measurable at this gate | No token or elapsed-time reading is taken when the review closes; both clocks are the Development Case's measurement policy and the gate is not instrumented |
| Anchor audit hit rate | 10 of 13 independently re-opened anchors held | The technical lens verified anchors rather than accepting them; the three that failed are the substance of F4 and the path in F1 of the Development Case |
| Open Major findings | 2 | Both hold the SBC milestone; neither holds this iteration's acceptance |
| Overdue findings | 0 | No deadline has passed since this review closed. The phase holds two review events and no finding is past its action |
| Trend across review events | 6 findings then 4; 0 Critical both times; 6 of 6 closures verified | Two events occurred, so this comparison is real. It is a count against a count, not a rate, and it says nothing about phase length |

The three "not measurable" entries are the correct reading, not missing data. Each names the measurement the phase cannot produce and why; filling one with a figure would be the failure the Development Case's measurement policy and `R006` exist to prevent. No trend is drawn beyond the two events that occurred, and no prior iteration or cycle is invented to manufacture one.

#### Verdict

**Iteration Accepted — the phase iterates.** The iteration read fifteen of its sixteen assigned areas and declared the sixteenth, met its objectives on the increment's own evidence, and both executing lenses accepted the work with changes. The four open findings are corrections to how the work is accounted for, not reasons to re-read an area, so the cut's next slice — the eleven areas of Survey i3 — stands unchanged.

The SBC milestone is not judged here and is not sanctioned. Its review opens once Survey's cut holds no unread area and every Major finding of the phase has its rework in the baseline. Neither condition is met: two Major findings are open from this review and forty-one areas of the cut remain across three iterations.

```plantuml
@startuml
title Survey iteration 2 - the verdict and what carries into iteration 3
hide empty members
skinparam classAttributeIconSize 0
skinparam wrapWidth 330
skinparam nodesep 30
skinparam ranksep 44

package "Iteration 2 - what it returned" {
  class "Objectives" as OBJ <<O1 to O5 met>> {
    O1 request path fixed : EP-003, EP-013, the allowlist, the route families
    O2 component plane : met with rework - F4 on the registry count
    O3 cross-cutting surfaces : six anchored, diagnostics declared unread
    O4 figures reconcile : every Coverage row reproduces from its enumeration
    O5 remainder cut : recorded with the process service
  }
  class "Slice" as SL <<reached>> {
    assigned : 16 areas, in the Work Order's order
    reached : 15
    declared not reached : pkg/diagnostics, unread in full, reason stated
  }
}

package "Findings open at the close of this review" {
  class "Major - 2" as MAJ <<holds the SBC gate>> {
    System Inventory#F4 : 15 categories against 13 anchored registries
    Iteration Plan#F3 : two risks placed on areas the plan declares unread
  }
  class "Minor - 2" as MIN <<owner's next turn>> {
    System Inventory#F1 : twenty-four counted against twenty-three rows
    Development Case#F1 : pkg/config/modes/ does not resolve
  }
  class "Critical - 0" as CRIT <<nothing escalates>> {
    stakeholder : asked nothing at an Iteration Acceptance
    REQUIRES_USER_INPUT : not raised
  }
}

package "What iteration 3 inherits" {
  class "The cut's own slice" as NEXT <<unchanged>> {
    areas : pkg/actors, pkg/runtime, pkg/placement, pkg/scheduler,
            pkg/sentry, pkg/security, pkg/operator, pkg/injector,
            pkg/acl, pkg/apis, pkg/client
    carried : pkg/diagnostics, unread in full this iteration
    note : no finding of this review adds or removes an area
  }
  class "Work owed before the gate" as WORK <<corrections, not re-reads>> {
    action 1 and 3 : the registry count and the risk it carries
    action 2 : the risk placement and the count drawn from it
    action 4, 5 : one stated figure, one cited path
  }
}

OBJ --> MAJ
SL --> NEXT
MAJ --> WORK
MAJ --> CRIT
MIN --> WORK
WORK --> NEXT

note bottom of CRIT
  An Iteration Acceptance asks the stakeholder nothing. Only an unresolved
  Critical finding goes to them, and both executing lenses recorded none.
end note
note bottom of NEXT
  Iteration 3's slice is what the cut already assigns, and pkg/runtime/
  lands there because the control planes iteration 3 reads are what its
  hot-reload, compstore, authorizer, scheduler and wfengine subtrees bind.
end note
@enduml
```

#### What the next iteration must show this office

| Check at the close of Survey i3 | Standard |
|---|---|
| Actions 1 to 5 | Each closed by its recording lens, verified against the corrected content, not against a note saying it was done |
| The two Major findings | Reworked before a role consumes the failing content: the registry set before Excavation's component recovery, the risk placement before the SBC gate reads it |
| The two Minor findings | Fixed in their owner's next turn, or re-recorded under their existing keys with a reason |
| Any finding that persists into i3 | Re-recorded under its existing key — never as a new finding, so review debt stays countable |
| Review debt | No finding past its deadline without a written escalation to the Project Manager |
| `pkg/diagnostics` | Read, or declared unread again with a reason — it is carried, not closed |
| New findings against i3's artifacts | Recorded with owner, severity and criterion before the iteration closes |
| The next review event | The Iteration Acceptance at the close of Survey i3, on the same boundary rule: the iteration's close triggers it, no date does |
