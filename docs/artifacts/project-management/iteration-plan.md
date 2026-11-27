## Document Control
Phase: Survey
Status: Draft — iteration 2 of the phase, initiated 2026-11-27. The cut assigns this iteration 16 areas; 57 areas remain across the three iterations that follow it. This plan carries the read figures the System Inventory's Coverage states, each reproduced from the files enumerated on its own row: iteration 1 opened 60 distinct files of the 620 its 13 areas hold, iteration 2 opened 28 it had not opened before, and the phase stands at 88 distinct files of the tree's 6 631 entries. No work-item size anywhere, because no phase has closed and there is no measured actual to size against; one iteration has closed and its measured spend is reported as that iteration's own fact, never as a rate. The SBC milestone is not judged.
Milestone Target: end-of-Survey milestone (SBC) — not yet achieved
## Iteration Objectives
Survey iteration 2 reads the sidecar's request path and the component plane it dispatches into. The unit of scope in the sprint zero is the **area of the repository**, not a use case: no use case enters this plan.

| # | Objective | Exit criterion |
|---|---|---|
| O1 | The request path is fixed: how an application's call becomes a call into a component, read from the code that does the dispatch | `go.sum` and `img` are recorded for what they are or are not; the request path is read at the depth its priority needs — `pkg/api/`'s two servers, its route families, the internal invocation server and the hand-maintained allowlist, plus `pkg/messaging/` and `pkg/channel/` — and every entry point the slice adds is labelled `EP-NNN` with its `Source:` anchor. An area opened short of its file count says so in Coverage, and that depth is Excavation's |
| O2 | The component plane is recorded: what the request path dispatches into and under what policy | `pkg/components/`, `pkg/resiliency/`, `pkg/middleware/` and `pkg/config/` are read; each registry is stated as the set of component kinds it admits, with the `Source:` anchor that declares the kind list |
| O3 | The cross-cutting surfaces the request path carries are anchored: observability, health, ports, modes, application health, CORS | `pkg/metrics/`, `pkg/healthz/`, `pkg/ports/`, `pkg/modes/`, `pkg/apphealth/` and `pkg/cors/` are read; each surface is stated with the anchor it was read from. `pkg/diagnostics/` is declared unread in full with its reason, and is not counted as reached |
| O4 | The phase's own account of itself is reconciled, so its figures can be reproduced from what it prints | Every Coverage row's stated count equals the files enumerated on that row; the outside-the-slice total equals the rows listed; the `docs` categories reconcile; the component and external-system counts are derived from the tables; the proto breakdown is consistent with `AN-002` and `AN-013`. This plan re-quotes the reconciled totals and nothing earlier |
| O5 | The remainder of the phase is cut and declared before any other role of the iteration reads | The cut is in this document and recorded with the process service, each area in exactly one iteration |

O1–O4 are the Software Archaeologist's reading and reconciliation, excepting the re-quote of reconciled figures, which is this role's. O5 is this iteration's act of initiation and is complete when the cut is recorded.

`pkg/api/` was named "read whole" in this objective when the iteration opened and was not read whole: Coverage records 6 of its 124 files, and `universal/` — the 29 files where each operation is implemented — is unread. The objective is restated on that fact rather than the fact on the objective. What the six files close is the perimeter: the entry points, the two gRPC servers, the public listener and the allowlist map. What they do not close is what an operation does, which is why the System Inventory ranks `pkg/api/` fourth for Excavation and the cut does not re-open the area.
## Plan and Milestones
| # | Work item | Owner | Input | Output | Exit condition |
|---|---|---|---|---|---|
| W0 | Tailor the process for the phase: Development Case, and the Contribution Guide the owner carries in-tree | Process Engineer | The IARI baseline; the project's declared facts; the owner's `CONTRIBUTING.md` | Development Case | The Environment stage of the inherited process is satisfied before the reading opens; every §5.2 optional is recorded fired or not fired with its reason |
| W1 | Declare the cut of this iteration and of the remainder | Project Manager | This plan; the repository tree | Cut recorded with the process service | The cut is recorded and no other role of the iteration works before it |
| W2 | Reconcile the Coverage counts and derive the stated counts from the tables | Software Archaeologist | System Inventory — its Coverage table, its Components and External actors tables, `AN-002`, `AN-013` | System Inventory — corrected Coverage, Document Control status line, Proto contracts row | Every Coverage row's stated count equals the files enumerated on it, the outside-the-slice total equals the rows listed, the `docs` categories reconcile; each stated count is derived from its table and stated once; the proto breakdown matches `AN-002` and `AN-013` |
| W3 | Read what the system says it is: the component registries as declarations | Software Archaeologist | `pkg/components/` | System Inventory — Documentation | Each registry is stated as the set of component kinds it admits, with the `Source:` anchor that declares the kind list; the fourteen category constants, the versioning and WASM gates and the pluggable host are recorded as declarations, not as behaviour |
| W4 | Fix the perimeter of the request path and enumerate the entry points it adds | Software Archaeologist | `go.sum`, `img`, `pkg/api/`'s servers, routes, internal invocation server and allowlist, `pkg/messaging/`, `pkg/channel/` | System Inventory — Perimeter | Every entry point the slice adds is enumerated and labelled `EP-NNN` with its `Source:` anchor; the hand-maintained gRPC allowlist map, the HTTP route families, the two gRPC servers, the public listener and the app-channel boundary are each stated with file and lines. `pkg/api/universal/` is not part of this read — it states what an operation does, which the perimeter does not need and Excavation does |
| W5 | Record what the request path and the plane do not hold together, and re-order what remains | Software Archaeologist | W2, W3, W4 outputs | System Inventory — Anomalies and Priorities | Each new anomaly is labelled `AN-NNN` with its anchor; the areas still to read are re-ordered against what this slice exposed |
| W6 | Score the risks this slice exposed, and name the role accountable for retiring each entry | Project Manager | System Inventory, the Risk List | Risk List | Every risk carries probability, impact, magnitude, strategy and an accountable role distinct from the register's custodian; every anomaly newly recorded here carries at least one risk; no risk is on `accept` at Significant or High magnitude without the stakeholder's granting answer |
| W7 | Re-quote the reconciled figures and restate the cut against what was read | Project Manager | W2's corrected counts; W4, W5 outputs; this plan | Iteration Plan | The measurement table carries the reconciled totals and nothing earlier; the cut matches the recorded cut area for area and in order; whether the cut still holds is answered from this iteration's reading |
| W8 | Review the iteration against the SBC criteria | Reviewer, Management Reviewer, Review Coordinator | Iteration increments | Review Record; milestone verdict | The verdict is issued; a Critical finding escalates to the stakeholder and the milestone does not close |
| W9 | Record the Iteration Assessment given the verdict | Project Manager | Review Record; this iteration's objectives and evaluation criteria | Iteration Assessment | Every objective is stated met or missed, with the scope or plan adjustment the next iteration takes |

No work item is given a size. One phase iteration has closed and its spend is recorded as that iteration's own measured fact — 19 480 197 tokens and 0:36:59.0766494 of agent time, with 0:00:00 of measured gate queue time — and it is not a rate: it is one iteration of a sprint zero whose reading is dominated by documents and manifests, so nothing is projected from it onto a slice that holds `pkg/api/` and the component plane. W2 exists because the phase's evidence that its reading happened must reproduce from the enumeration printed beside it.
## Resources
| Role | Discipline | Stages this iteration | What it produces |
|---|---|---|---|
| Process Engineer | Environment | The Environment stage the inherited process runs at the head of the iteration — Development Case and Contribution Guide | Development Case — W0, an explicit work item so that an artifact of the increment is never absent from the plan that reports on it |
| Project Manager | Discovery, Project Management | Cut; then Risk List and Iteration Plan; then the Iteration Assessment after the verdict | Iteration Plan, Risk List, Iteration Assessment |
| Software Archaeologist | Discovery | Reconcile the counts; Documentation; Perimeter; Anomalies and Priorities | System Inventory |
| Reviewer | — | Milestone review | Review Record findings against the SBC criteria |
| Management Reviewer | — | Milestone review | Investment assessment of the phase |
| Review Coordinator | — | Milestone review | The milestone verdict |

Roles that do **not** work in Survey: System Analyst, Requirements Specifier, Software Architect, Designer, Database Designer, User Interface Designer, Capsule Designer, Implementer, Integrator, Test Manager, Test Analyst, Test Designer, Tester, Deployment Manager, Technical Writer, Configuration Manager, Change Control Manager, Business Process Analyst, Business Reviewer, Code Reviewer. Their first phase is Excavation or later, per the inherited process. The Business Process Analyst and the Business Reviewer do not work in any phase (Development Case §2); the Capsule Designer does not either (§3).

No role is added to the phase to shorten it. Parallelism across the reading would put two roles on the same area, and the second would be re-reading what the first already wrote. The one role at work on the reading this iteration is the Software Archaeologist, because the slice is one connected surface — the request path and the plane it dispatches into — and splitting it would split a fact between two accounts.

## Use Cases and Scenarios Addressed

None. No use case enters the plan in Survey: the phase's scope unit is the area of the repository listed in the cut above. Use cases are recovered in Excavation from the entry points this phase labels `EP-NNN` — the Use-Case Model traces each recovered use case to the entry point that performs it.

| Phase | Scope unit | Where it is planned |
|---|---|---|
| Survey | Repository area | This plan, "Coarse roadmap — the cut of the sprint zero" |
| Excavation | Entry point `EP-NNN`, recovered use case | The Excavation Iteration Plan |
| Consolidation and later | Declared use case, risk-driven selection | Each phase's Iteration Plan, chosen by risk and coverage |

## Evaluation Criteria
| Criterion | Threshold | Evidence |
|---|---|---|
| Request path fixed | `go.sum` and `img` recorded for what they are or are not; `pkg/api/`, `pkg/messaging/` and `pkg/channel/` read at the depth their priority needs; every entry point the slice adds is labelled `EP-NNN` with its `Source:` anchor, and an area opened short of its file count says so | System Inventory — Perimeter and Coverage, this iteration's rows |
| Component plane recorded | Each registry under `pkg/components/` is stated as the set of component kinds it admits, with the anchor that declares the kind list; `pkg/resiliency/`, `pkg/middleware/` and `pkg/config/` are stated with their anchors | System Inventory — Documentation and Perimeter |
| Cross-cutting surfaces anchored | `pkg/diagnostics/`, `pkg/metrics/`, `pkg/healthz/`, `pkg/ports/`, `pkg/modes/`, `pkg/apphealth/` and `pkg/cors/` each stated with the file and lines read, or declared unread with its reason | System Inventory — Perimeter and Coverage |
| Counts reproduce from their enumeration | Every Coverage row's stated count equals the files enumerated on that row, the outside-the-slice total equals the rows listed, and the `docs` categories reconcile. The component and external-system counts are derived from their tables and stated once, and the proto breakdown matches `AN-002` and `AN-013` | System Inventory — Coverage, Document Control, Components and External actors tables, Proto contracts row |
| Plan re-quotes only reproduced figures | Every figure this plan carries is taken from a Coverage row that reproduces from its own enumeration, and is quoted with the iteration that opened it. A count that cannot be reproduced from its enumeration is counted again, not restated | This plan's measurement table, cut tables and "The remainder, counted" |
| Cut held | Every area listed in the cut belongs to exactly one iteration, in the order the cut records, and no area of the tree is left uncut | This plan's cut tables and the recorded cut — 70 areas, each in exactly one iteration |
| Unread areas declared | An area the iteration does not reach is named in the next iteration's row, not counted as reached; the remainder is stated as the counts the cut holds | This plan's cut tables: 57 areas across the four remaining iterations, `go.sum` and `img` among this iteration's |
| Risks sequenced and accountable | Every risk of the Risk List names the iteration of the cut that confronts it, and the role accountable for retiring it, distinct from the register's custodian | This plan's "Risk-driven sequencing"; Risk List — every anomaly carried, every High and Significant risk on `avoid` |
| No figure the process did not measure | No work-item size, no rate, no phase-length forecast and no calendar date appears in this plan. What this system measures is reported as measured: an iteration's token spend, its elapsed agent time and its measured gate queue time, with the clocks apart and never summed. One iteration has closed and its actual is reported as that iteration's fact; it is not projected onto this iteration's slice, and no phase has closed, so the risk list carries no phase-level spend | This plan; the Risk List's R006; the Iteration Assessment's measurement table |

Each criterion is checkable against an artifact in the increment.

**What this iteration measures, and the decision each measurement serves.**

| Measurement | Value | Decision it enables |
|---|---|---|
| Areas of the slice reached | 16 assigned, 15 of them yielding a file; `pkg/diagnostics/` declared unread in full | Whether an area is read at the depth its priority needs, or skimmed — and, where short, whether that depth belongs to Excavation instead |
| Files opened against the files the slice areas hold | Iteration 2 opened 20 files it had not opened before across its own slice, plus 2 it re-opened and 8 outside the slice; 28 distinct files it had not opened before. Across the phase: 88 distinct files of the 6 631 entries the tree holds — 60 from iteration 1, 28 from iteration 2 | Whether the perimeter can be closed from the slice alone; a rising count of files opened outside the slice means the cut is drawn in the wrong place |
| Anomalies recorded, and the new ones this slice added | 23 at the close, of which 3 are this iteration's: `AN-021`, `AN-022`, `AN-023` | Whether the reading is finding the system's incoherences or only describing it, and whether a slice is large enough to expose any |
| Risks carrying no iteration against them | 0, checked when the Risk List is scored at W6 — 28 risks, each placed on an iteration or on the whole reading | Whether the cut is ordered by risk at all, or only by convenience of location |

The read figures are the System Inventory's Coverage rows, each of which reproduces from the files enumerated on it; iteration 1's total is 60 distinct files from 13 areas, not the sum the phase first printed. No rate is derived from any of these. They are counts of what an iteration read — not a velocity, and not a basis for projecting the length of the phase. Whether the cut suffices is asked at each iteration close from what that iteration read.
