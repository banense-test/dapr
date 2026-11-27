## Document Control
Phase: Survey
Status: Draft — iteration 2 assessment, recorded after the Review Coordinator's verdict. The iteration is accepted and the phase iterates; the SBC milestone is not judged and no phase is closed
Milestone Target: end-of-Survey milestone (SBC) — not achieved

## Iteration Objectives Reached
Survey iteration 2 is assessed against the five objectives its Iteration Plan committed to and against the phase's three intended objectives. The Review Coordinator's verdict is recorded here as given: **SBC — iteration REQUIRED**. This document records that verdict; it does not issue a milestone.

| # | Objective committed in the Iteration Plan | Owner | Outcome | Evidence, and what qualifies it |
|---|---|---|---|---|
| O1 | The request path is fixed: how an application's call becomes a call into a component, read from the code that does the dispatch | Software Archaeologist | **Met** | System Inventory — Perimeter and Coverage: `go.sum` and `img` recorded for what they are, `EP-003` and `EP-013` anchored on `pkg/api/http/server.go`, the allowlist read from `pkg/api/grpc/endpoints.go`, `pkg/messaging/v1/messaging.go` and `pkg/channel/channel.go` read. `pkg/diagnostics/` declared unread in full with its reason, not counted as reached. No finding is recorded against this content by either executing lens |
| O2 | The component plane is recorded: what the request path dispatches into and under what policy | Software Archaeologist | **Met, with rework owed** | `pkg/components/`, `pkg/resiliency/`, `pkg/middleware/` and `pkg/config/` were read and each anchor holds — the Technical lens opened `state/`, `lock/` and `conversation/registry.go` and all three follow the pattern the row describes. Qualified by `System Inventory#F4`, Major: fifteen `Category` constants against thirteen anchored registry paths, with `workflow` and `workflowbackend` resolving to nothing. The Review Coordinator's C1 resolved the cross-lens question — the management lens passed O2 on the artifact's statement, the technical lens opened the tree, and the opened file governs. The registry set is therefore recorded as thirteen located and two unlocated, carried as `R029` |
| O3 | The cross-cutting surfaces the request path carries are anchored | Software Archaeologist | **Met** | `pkg/metrics/exporter.go` (`EP-012`), `pkg/healthz/healthz.go` (`EP-011`), `pkg/ports/ports.go`, `pkg/modes/modes.go`, `pkg/apphealth/health.go`, `pkg/cors/cors.go` — each read and each stated with its anchor. The Technical lens opened `healthz.go`, `modes.go` and `cors.go` and all three hold. `pkg/diagnostics/` (35 files) declared unread in full and not counted as reached |
| O4 | The phase's own account of itself is reconciled, so its figures can be reproduced from what it prints | Software Archaeologist, and this role for the re-quote | **Met, one count open** | `System Inventory#F2` and `#F3` closed this review by their recording lens on verified evidence — every Coverage row re-added by hand, 60 + 28 = 88 for the phase, 42 / 19 / 16 derived from the tables. This plan re-quotes only those reconciled figures. Qualified by `System Inventory#F1` under the Management Reviewer's lens, Minor: "twenty-four areas never assigned" against twenty-three rows at the foot of the Coverage table. No objective fails on it and no quoted total changes |
| O5 | The remainder of the phase is cut and declared before any other role of the iteration reads | Project Manager | **Met** | The cut is recorded with the process service: 16 areas of this iteration, 41 across the three that follow (11 + 17 + 13), 70 areas each in exactly one iteration. No area is in two iterations, and `go.sum` and `img` appear in this iteration's row only |

| Phase objective this iteration serves | Served? | Where it stands after the verdict |
|---|---|---|
| Confirm the System Boundary | Partly — two of the phase's five cut iterations are read | The boundary is enumerated and anchored over 29 of 70 areas; three iterations of the cut are unread, so it is not yet confirmed. `pkg/api/universal/` — the 29 files where each operation is implemented — is deliberately outside this read and is ranked fourth for Excavation |
| Tailor Development Process | Not an objective of this iteration; stands as delivered | The Development Case was carried as W0 and delivered before the reading opened. It carries `Development Case#F5` (Minor) from the Technical lens — one evidence path, `pkg/config/modes/` against the actual `pkg/modes/modes.go` — which does not touch the Deployment Model verdict: FIRED rests on seven subcharts, eight Dockerfiles, six executables, two runtime modes, placement and scheduler as separate control planes, and mTLS through Sentry |
| Identify Critical Risks | Yes, for what was read | 29 risks, every one of the 23 anomalies carried, three High and fifteen Significant all on `avoid`, three accepted at Moderate. Four entries were added this iteration: `R026`, `R027`, `R028` from this slice's anomalies and `R029` as this role's consuming end of `System Inventory#F4`. None is retired and none is due to: the phase has no closed iteration behind it that produced a second reading |

Five committed objectives: three met, two met with rework owed inside an artifact. No objective was missed, and no defect touches what the iteration read.

```plantuml
@startuml
title Survey iteration 2 - the close of the iteration, from the facts to what iteration 3 takes
skinparam wrapWidth 240

|Facts of the iteration|
start
:Sixteen areas assigned, fifteen reached;\npkg/diagnostics declared unread in full;
:Twenty-eight distinct files opened that\niteration 1 had not; three re-opened;
:Three anomalies added - AN-021, AN-022, AN-023;
|Review lenses|
:Technical lens executed;\nF4 Major on the component plane,\nF5 Minor on the Development Case;
:Management lens executed;\nF3 Major on risk placement,\nF1 Minor on a Coverage count;
:Business lens INACTIVE - no verdict attributed to it;
note right
  Business Modeling is inactive for this
  project, so no business verdict exists
  and none is reported.
end note
|Review Coordinator|
:Milestone verdict - SBC: iteration REQUIRED;
note right
  Recorded here, not decided here. Two Major
  findings stand open and the cut still holds
  three unread iterations.
end note
|Project Manager|
:Resolve this role's own end of the record:\nrestate the sequencing row, the criteria\nand the placed count;
:Add R029 - the two component categories\nno read has located;
:Assess the phase's three objectives and\nthe nine criteria the plan carried;
:Record each variance with the correction\nit forces in iteration 3;
:Iteration Assessment - this artifact;
stop
@enduml
```

## Adherence to Plan
The fine plan committed ten work items, W0 to W9. All ten ran, in the order written, and the tenth — this document — records the outcome.

## Use Cases and Scenarios Implemented
None, and this is the phase working as designed rather than an omission. No use case enters a Survey plan: the scope unit of the sprint zero is the area of the repository, and this iteration's unit was the sixteen areas the cut assigned it. No Test discipline runs in Survey, so no scenario was executed and no executable was produced.

What the iteration returned instead is the raw material a use case will be recovered from in Excavation. This slice fixed the request-path entry points — `EP-003` and `EP-013` on `pkg/api/http/server.go` across its two listeners, `EP-011` on `pkg/healthz/healthz.go`, `EP-012` on `pkg/metrics/exporter.go` — and recorded the operations the app-channel boundary offers in `pkg/channel/channel.go`. `pkg/api/universal/`, where each operation is implemented, is deliberately outside this read: what an operation does is Excavation's, and it is ranked fourth in the System Inventory's Priorities.

| Phase | Scope unit | This iteration |
|---|---|---|
| Survey | Repository area | 29 of 70 areas of the cut read over two iterations; 15 of this iteration's 16 areas reached |
| Excavation | Entry point `EP-NNN`, recovered use case | Not started — the request-path entry points and the app-channel boundary are its input |
| Consolidation and later | Declared use case, risk-driven selection | Not started |

## Results Relative to Evaluation Criteria
Each of the nine criteria the Iteration Plan carried, answered against the evidence that exists after the review. Seven met, two met with rework owed, one not met as written and corrected this turn.

```plantuml
@startuml
title Evaluation criteria of Survey iteration 2 - outcome against each
hide empty members
skinparam classAttributeIconSize 0
skinparam wrapWidth 320
skinparam nodesep 20
skinparam ranksep 34

class "Request path fixed" as C1 <<met>> {
  evidence : EP-003 and EP-013 anchored
  evidence : pkg/messaging, pkg/channel read
  evidence : go.sum and img recorded for what they are
}

class "Component plane recorded" as C2 <<met with rework owed>> {
  evidence : thirteen registries stated with anchors
  defect : System Inventory#F4 - fifteen constants
  defect : System Inventory#F4 - two unlocated
  carried : R029
}

class "Cross-cutting surfaces anchored" as C3 <<met>> {
  evidence : metrics, healthz, ports, modes,
             apphealth, cors each anchored
  evidence : pkg/diagnostics declared unread in full
}

class "Counts reproduce" as C4 <<met, one count open>> {
  evidence : F2 and F3 closed by their lens
  defect : System Inventory#F1 - 24 against 23
}

class "Plan re-quotes only reproduced figures" as C5 <<met>> {
  evidence : 28 files this iteration, 88 for the phase
}

class "Cut held" as C6 <<met>> {
  evidence : 70 areas, each in exactly one iteration
}

class "Unread areas declared" as C7 <<met>> {
  evidence : 16 assigned, 15 reached
  evidence : pkg/diagnostics named and carried
}

class "Risks sequenced and accountable" as C8 <<not met as written>> {
  was : R017 and R019 on areas the plan declares unread
  now : R019 moved to i3; R017 claims no unopened area
  rework : Iteration Plan#F3
}

class "No figure the process did not measure" as C9 <<met>> {
  evidence : no size, no rate, no date in the plan
  evidence : one iteration's spend, both clocks apart
}

class "Outcome" as O <<the iteration's own verdict>> {
  met : C1, C3, C5, C6, C7, C9
  met with rework : C2, C4
  not met as written : C8, corrected this turn
}

C1 -down-> O
C2 -down-> O
C3 -down-> O
C4 -down-> O
C5 -down-> O
C6 -down-> O
C7 -down-> O
C8 -down-> O
C9 -down-> O

note bottom of O
  No failure changes what the iteration read: C2, C4 and C8
  are corrections to how an artifact accounts for it, and
  C8's correction is applied in the plan this turn.
end note
@enduml
```

| Criterion | Threshold | Outcome | Evidence |
|---|---|---|---|
| Request path fixed | `go.sum` and `img` recorded for what they are; the request path read at the depth its priority needs; every entry point the slice adds labelled `EP-NNN` with its `Source:` anchor | Met | System Inventory — Perimeter and Coverage: `EP-003` and `EP-013` on `pkg/api/http/server.go`, the allowlist on `pkg/api/grpc/endpoints.go`, `pkg/messaging/v1/messaging.go`, `pkg/channel/channel.go`. `go.sum` recorded as a closed module graph, `img` as three files stating no fact about the perimeter |
| Component plane recorded | Each registry stated as the set of component kinds it admits, with the anchor that declares the kind list; `pkg/resiliency/`, `pkg/middleware/` and `pkg/config/` anchored. Complete only if the count holds | **Met, with rework owed** | Thirteen registries anchored and stated; `state/`, `lock/` and `conversation/` opened for the review and all three follow the pattern. `System Inventory#F4`: fifteen `Category` constants, and `pkg/components/workflow/registry.go` and `workflowbackend/registry.go` resolve to nothing. Carried as `R029` |
| Cross-cutting surfaces anchored | `pkg/metrics/`, `pkg/healthz/`, `pkg/ports/`, `pkg/modes/`, `pkg/apphealth/`, `pkg/cors/` each stated with file and lines; `pkg/diagnostics/` declared unread in full with its reason | Met | All six read and anchored; `healthz.go`, `modes.go` and `cors.go` opened for the review and each holds. `pkg/diagnostics/` (35 files) declared unread, not counted as reached |
| Counts reproduce from their enumeration | Every Coverage row's stated count equals the files enumerated on it; the outside-slice total equals the rows listed; the `docs` categories reconcile; each stated count derived from its table and stated once | **Met, one count open** | `System Inventory#F2` and `#F3` closed by their recording lens on hand re-addition: 60 + 28 = 88 for the phase, 42 / 19 / 16 derived from the tables, proto at 37 + 7 + 1 = 45. `System Inventory#F1` (Minor, Management Reviewer) on the never-assigned sentence: twenty-four asserted, twenty-three rows |
| Plan re-quotes only reproduced figures | Every figure taken from a Coverage row that reproduces from its own enumeration, quoted with the iteration that opened it | Met | 28 distinct files this iteration, 88 for the phase, quoted from the reconciled rows with iteration 1's 60 named beside them. No count is stated that its own row does not reproduce |
| Cut held | Every area in exactly one iteration, in the order recorded, none left uncut | Met | 70 areas — `.iari/` excluded as not part of the system — each in exactly one iteration; 16 here, 41 across the three that follow, 13 read at i1 |
| Unread areas declared | An area the iteration does not reach is named in the next iteration's row, not counted as reached; the remainder stated as the counts the cut holds | Met | 16 assigned, 15 reached; `pkg/diagnostics/` named with its reason and carried to Excavation's Priorities order at priority 4 |
| Risks sequenced and accountable | Every risk names the iteration that confronts it and the role accountable for retiring it; a placement names an area that iteration opens | **Not met as written** — corrected this turn | `Iteration Plan#F3`: R017 rested on `pkg/api/universal/` and R019 on `pkg/components/`, both declared unread here, and R019's mitigation names `pkg/scheduler/`, which i3 reads while omitting the risk. Corrected in the plan this turn to R017 and R028 on `pkg/api/` and R019 on i3; 29 risks placed, none on an unopened area |
| No figure the process did not measure | No work-item size, no rate, no phase-length forecast, no calendar date; measured figures reported with both clocks apart and never summed | Met | No size, rate or date anywhere in the plan or this document; the one measured actual is iteration 1's, and this iteration's own figures are in the table below, each clock apart |

**What this iteration measured, and the decision each measurement enables.** The figures below are the iteration's recorded facts, not a velocity and not a baseline. No rate is derived from any of them, and none is projected onto iteration 3.

| Measurement | Value | Decision it enables |
|---|---|---|
| Agent work — token spend | 30 715 328 tokens | This iteration's actual. Read against iteration 1's 19 480 197 it shows what a slice costs when it re-reads the accumulated surface: this iteration read 16 areas of a larger system but carried a 236 000-character artifact surface into every role, where iteration 1 carried 128 000. The decision it enables is where the next phase's cost sits — re-reading, not emission — and that is why the plan declares no phase spend before one closes |
| Agent work — measured elapsed time | 0:48:19.7330924 | Read beside the token figure, both clocks on agent work and apart from the gate. It is 11 minutes longer than iteration 1's 0:36:59.0766494 on a slice of comparable area count, and the difference is the surface the roles re-read. The decision: whether the cut should take fewer, larger areas per iteration or more, smaller ones — asked at each iteration close, never projected |
| Human gate — queue time | 0:00:00 measured; the end-of-iteration approval gate is excluded from the measurement | The gate cost nothing in queue time here. It does not lower `R002`'s 14-day ceiling: that ceiling is a bound on a gate, and the next one's queue is measured when it opens |
| Artifacts in the increment | 6 | The six the Development Case sanctions this phase iteration: Development Case, System Inventory, Iteration Plan, Risk List, Review Record, Iteration Assessment. The Glossary and Deployment Model are FIRED for later phases and their absence here is correct |
| Artifact sizes, in characters | Development Case 28 911; Iteration Plan 39 760; Risk List 44 072; System Inventory 121 936; Review Record 88 547; Iteration Assessment 26 636 | Names where this iteration's spend and the next phase's re-reading cost sit. The System Inventory at 121 936 is the largest and grew 55 551 characters on one slice: it is the artifact every later phase and both lenses re-read in full, so Excavation's cost is set by this surface, not by the code it will emit. The decision it enables: the inventory's growth per slice is the figure the cut's remaining three iterations are read against when the question of a further iteration arises under `R001` |
| Stakeholder interactions | 11 | Confirms the iteration resolved its questions in one round rather than by attrition; no question was asked this iteration that the Development Case had already settled. A rising count in a later iteration means a scope question is being deferred, not answered |
| Areas of the slice reached | 16 assigned, 15 reached; `pkg/diagnostics/` unread in full | Whether an area is read at the depth its priority needs, or skimmed — and where short, whether that depth belongs to Excavation. This iteration's answer: it does, by the Priorities order |
| Component categories the plane accounts for | 13 of 15 located; `workflow` and `workflowbackend` unlocated | Whether the component view can be taken from `pkg/components/` alone. It cannot, and `R029` carries the consequence until Survey i3 reads the two subtrees that hold the registration |

Two figures in the table are quoted from the system's own measurement of this iteration — token spend and elapsed agent time. The remaining rows are counts of files, areas, artifacts and interactions read from the increment itself. Neither group is a rate, and the gate's queue time is reported apart from both and never added to either.

## Test Results

No test was designed, executed or reported in Survey iteration 1. No Test discipline runs in this phase: the Test Manager's first stage is the Inception Test Plan, and the Test Designer, Tester and Test Analyst first work in Elaboration.

No test figure is available and none is asserted — no pass rate, no coverage percentage, no build status. The test substrate was named, not run: `tests/` holds 3 335 entries and is Survey iteration 4's slice, and the anomalies touching it — `AN-008` (two suites disabled by renaming), `AN-019` (156 registration files against one test), `AN-020` (harness parsing free text) — are recorded as what the tree shows, not as results of any execution.

## External Changes

One external change is in force: the stakeholder answered the Process Engineer's question on the root `CONTRIBUTING.md`, granting one pointer line at its top to `docs/CONTRIBUTING.md` for how a change is requested, labelled and reviewed. The answer is applied and both review lenses verified it — the pointer line present, the owner's text below it unchanged.

No change request was opened or decided: the Change Control Manager does not work in the sprint zero, so the stakeholder's order reached its owner through the work order instead of a ledger entry. No pull request exists to dispose of — no role of Survey produces code, and the SCM holds no PR in any state.

The stakeholder is asked nothing by this assessment. The verdict escalates only an unresolved Critical finding, and both executing lenses recorded zero.

## Rework Required
Four Major findings and two Minor stand open at the close of the review. All findings against this role's own artifacts — `Iteration Plan#F1`, `Iteration Plan#F2` and `Risk List#F1` — have their rework applied this turn; the three against the System Inventory are the Software Archaeologist's and are owed.

```plantuml
@startgantt
title What the phase owes before iteration 2's cut is declared - order only, no duration claimed
hide footbox

[Reconcile the Coverage counts - Software Archaeologist] lasts 1 day
[Delete the phase-length quotient and restate the slice row - Project Manager] starts at [Reconcile the Coverage counts - Software Archaeologist]'s end
[Re-quote the corrected totals in the plan - Project Manager] starts at [Delete the phase-length quotient and restate the slice row - Project Manager]'s end
[Add the accountable-role column to the register - Project Manager] starts at [Re-quote the corrected totals in the plan - Project Manager]'s end
[Iteration 2 cut declared - Project Manager] starts at [Add the accountable-role column to the register - Project Manager]'s end
[Survey i2 - request path and component plane] starts at [Iteration 2 cut declared - Project Manager]'s end
[Iteration Acceptance - Survey i2] starts at [Survey i2 - request path and component plane]'s end
[SBC gate - not open] starts at [Iteration Acceptance - Survey i2]'s end

[SBC gate - not open] is colored in Orange

note bottom
  Every bar is drawn the same length: no duration has been measured and
  no phase has closed, so no bar length and no date is claimed. The orange
  bar is queue time, bounded at 14 days in the Risk List under R002 and
  measured when it opens - never forecast, never added to agent time.
  The SBC gate opens only when every area of the cut is read and every
  Major finding of the phase has its rework in the baseline.
end note
@endgantt
```

| # | Rework | Owner | Closes | Holds the SBC gate | State |
|---|---|---|---|---|---|
| 1 | Delete the 57 ÷ 13 quotient and its conclusion; state the remainder as the counts the cut holds, with `R001` as the risk and one more iteration as the remedy | Project Manager | `Iteration Plan#F1` | Yes | Applied to the Iteration Plan this turn; closure is the Management Reviewer's |
| 2 | Restate iteration 1's row as 13 assigned and 13 reached; list `go.sum` and `img` in iteration 2's row only; restate the "Unread areas declared" criterion | Project Manager | `Iteration Plan#F2` | Yes | Applied to the Iteration Plan this turn; closure is the Management Reviewer's |
| 3 | Add an accountable-role column distinct from the register's custodian, naming the role that performs the retiring act | Project Manager | `Risk List#F1` | No | Applied to the Risk List this turn; closure is the Management Reviewer's |
| 4 | Reconcile every Coverage row's stated count with the files it enumerates; correct the outside-slice total and the `docs` categories | Software Archaeologist | `System Inventory#F2` | Yes | Owed — before the next cut sizes itself from these counts |
| 5 | Anchor the actor-state start-up requirement on code that enforces it, or restate the claim as what `actorStateStoreBindings` does; move the anchor out of the declared unread set | Software Archaeologist | `System Inventory#F1` | Yes | Owed — the iteration that reads `pkg/runtime/`, Survey i3 by the cut |
| 6 | Derive the component and external-system counts from the tables; restate the proto file breakdown consistent with `AN-002` and `AN-013` | Software Archaeologist | `System Inventory#F3` | No | Owed — owner's next turn |

Items 4 and 5 are why the SBC review's entry criterion is unmet and why the phase iterates. Item 5's remedy is placed in the iteration the cut already assigns `pkg/runtime/` to — Survey i3 — so the claim is anchored where the code that enforces it is read, not re-opened inside iteration 2's slice.

### What iteration 2 takes

The cut's next slice, unchanged: `go.sum` and `img` as its own areas, then `pkg/api/` read whole, `pkg/messaging/`, `pkg/channel/`, `pkg/components/`, `pkg/resiliency/`, `pkg/middleware/`, `pkg/config/`, `pkg/diagnostics/`, `pkg/metrics/`, `pkg/healthz/`, `pkg/ports/`, `pkg/modes/`, `pkg/apphealth/`, `pkg/cors/`. No finding of this review adds an area to it or removes one: the four Major findings are corrections to how iteration 1 is accounted for, not reasons to re-read an area. It inherits all sixteen Significant and High risks, unretired and not yet due, and it carries the Environment stage as its first work item. Its two Minor findings are fixed in their owner's next turn; any defect that persists is re-recorded under its existing key, never as a new one.

The re-quote of the corrected Coverage totals — in the Iteration Plan's measurement table, its Evaluation Criteria and its cut tables — cannot be applied before item 4 lands. Until then the plan states no such figure, which is the correct state and not a gap: a count that cannot be reproduced from its own enumeration is corrected at source first, then quoted.

