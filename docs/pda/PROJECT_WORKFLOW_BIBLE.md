# Project Workflow Bible

**Version:** 1.0.0  
**Status:** Development blueprint  
**Canonical machine source:** [`workflow_processes.yaml`](./workflow_processes.yaml)

This document defines **every process** in the project delivery automation (PDA) platform: lifecycle stages, gates, cross-cutting controls, closeout, and platform services. Nothing advances without satisfying entry/exit rules (**NO GATE → NO PROGRESSION**), except via **authorised override** with a full audit trail.

---

## 1. Purpose

1. Prevent omission of critical PM and project-control activities during product build.  
2. Drive a single implementation path: **workflow spec → data model → schema → API → rules → automation → AI layer → UI → reports → notifications → audit**.  
3. Align sequential **stages** with parallel **cross-cutting domains** (risk, issue, change, decision, dependency, cost, schedule, quality, stakeholders, communications).

---

## 2. Master lifecycle (sequential)

Defined in [`project_lifecycle.yaml`](./project_lifecycle.yaml). Nineteen ordered steps: intake through benefits realisation, with five **gates** (business case, authorisation, scope, plan, execution readiness).

**Parallel note:** `MONITORING_CONTROL` runs alongside `EXECUTION`; control engines are event-driven for the whole authorised-to-close period (see [`cross_cutting_domains.yaml`](./cross_cutting_domains.yaml)).

---

## 3. Every stage: five layers (+ gate extras)

Each stage in [`stages/lifecycle_stages.yaml`](./stages/lifecycle_stages.yaml) must implement:

| Layer | Purpose |
|--------|---------|
| **Entry conditions** | What must be true before work starts |
| **Required inputs** | Data and artifacts consumed |
| **Activities** | Work performed |
| **Required outputs** | Artifacts produced |
| **Exit conditions** | Proof the stage is complete |

Where applicable: **approval**, **validation**, **escalation**, **mandatory_artifacts**, **mandatory_approvals**, **validation_rules**.

Advancement logic: [`rules/advancement.yaml`](./rules/advancement.yaml).

---

## 4. Process matrix column definitions

| Column | Meaning |
|--------|---------|
| **Stage** | Lifecycle or domain grouping |
| **Process** | Named activity |
| **PM Role** | Primary accountable role |
| **Input** | What triggers the work |
| **Activity** | What is done |
| **Output** | Artifact or state change |
| **Required Data** | Fields or entities that must exist |
| **Validation** | Rules that must pass |
| **Gate** | Yes = decision or hard block on progression |
| **Automation** | System behaviour (calc, enforce, generate) |
| **Trigger** | Event or user action that starts the process |
| **Alert** | Notification when attention needed |
| **Escalation** | Who receives elevated visibility |
| **Report** | Where outcome surfaces (PCR = Project Control Report §) |

---

## 5. Ultimate workflow logic (WHY → benefit)

```text
WHY?          → Business Case
WHAT?         → Charter
WHAT EXACTLY? → Requirements
WHAT'S IN?    → Scope (+ WBS)
HOW/WHEN/WHO? → Integrated Plan → PROJECT BASELINE
READY?        → Mobilisation → Gate 05
EXECUTE       → Actuals + Events + Changes
ANALYSE       → Control → Impact → Forecast → Priority
ACT           → PM actions / escalation
VERIFY        → Commission → Accept → Handover → Close
VALUE         → Benefits realisation (post-close)
```

Entity graph: [`data_relationships.yaml`](./data_relationships.yaml).

---

## 6. Event-driven execution (example)

One external event fans out automatically (see [`events/event_catalog.yaml`](./events/event_catalog.yaml)):

```text
Vendor misses delivery
  → affected activities → dependencies → critical path
  → milestones → cost → risks → forecast → severity
  → issue/risk update → notify owner → PM action
  → escalate if needed → health → PCR sections
```

Process row: **PRC-PLT-003** in `workflow_processes.yaml`.

---

## 7. Process register (summary table)

Full rows (IDs **PRC-***): **`workflow_processes.yaml`**. Below: representative rows matching the bible column set; expand the YAML for implementation tickets.

### 7.1 Intake & business case

| Stage | Process | PM Role | Input | Activity | Output | Required Data | Validation | Gate | Automation | Trigger | Alert | Escalation | Report |
|-------|---------|---------|-------|----------|--------|---------------|------------|------|------------|---------|-------|------------|--------|
| Project Intake | Capture request | PM / Unit | Idea | Record intake | Draft record | project_name, problem_statement, business_need, sponsor, … | Required fields | No | Duplicate scan | Submit | Missing sponsor | Portfolio | Intake |
| Project Intake | Classify initiative | PM | Intake | Project vs BAU | Classification | problem, need | problem_defined | No | Rules / AI | Intake complete | Ambiguous | Sponsor | Intake |
| Project Intake | Intake gate | Sponsor | Concept record | ACCEPT/REJECT/… | Decision | PROJECT_CONCEPT_RECORD | System checks | **Yes** | Block advance | Submission | Pending | Board | Gate log |
| Business Case | Options analysis | Sponsor/PM | Problem | Assess options | Preferred option | options, costs, benefits | options_considered | No | AI compare | New option | Missing analysis | Sponsor | BC |
| Business Case | Financial case | PM/Finance | Estimates | Build case | Financial section | costs, funding, benefits | costs_estimated | No | NPV tools | Cost update | No funding | Finance | BC |
| Gate 01 | BC approval | Authority | Complete BC | APPROVE/REWORK/… | Gate record | gate required list | All true | **Yes** | REWORK → BC | BC submit | Pending | Executive | Gate log |

### 7.2 Charter, requirements, scope

| Stage | Process | PM Role | Input | Activity | Output | Required Data | Validation | Gate | Automation | Trigger | Alert | Escalation | Report |
|-------|---------|---------|-------|----------|--------|---------------|------------|------|------------|---------|-------|------------|--------|
| Charter | Charter from BC | PM | Approved BC | Inherit + govern | Draft charter | roles, scope, budget | No re-entry | No | Variance flags | G01 APPROVE | BC drift | Sponsor | Charter |
| Gate 02 | Authorisation | Sponsor | Charter | Confirm budget/roles | AUTHORISED | authorisation checklist | All true | **Yes** | Set status | Submit | No PM | Executive | Auth register |
| Discovery | Gather requirements | BA/PM | Need | Elicit by category | REQ-* register | categories | IDs unique | No | AI completeness | Req change | Unmapped | PM | Trace matrix |
| Discovery | Traceability | BA/PM | Req + deliverables | Link chain | Trace graph | links | Orphans blocked | No | Graph validate | Scope change | Orphan req | PM | Trace matrix |
| Scope | Scope baseline | PM | Requirements | In/out, deps | SCOPE_BASELINE | in/out, assumptions | exclusions | No | Diff vs charter | Edit | Creep | Sponsor | Scope |
| Scope | WBS | PM | Deliverables | Decompose | WBS | WPs | WBS complete | No | Integrity | New deliverable | Missing WP | PM | WBS |
| Gate 03 | Scope validation | PM/SH | Baseline+WBS | Run checks | Approved or list | scope_validation.* | All pass | **Yes** | DO NOT PROCEED | Submit | Missing list | Sponsor | Scope gate |

### 7.3 Planning & mobilisation

| Stage | Process | PM Role | Input | Activity | Output | Required Data | Validation | Gate | Automation | Trigger | Alert | Escalation | Report |
|-------|---------|---------|-------|----------|--------|---------------|------------|------|------------|---------|-------|------------|--------|
| Planning | Schedule | PM | WBS | CPM, baseline | Schedule | activities, deps | CP identified | No | Float monitor | Change | Float breach | PM | Schedule |
| Planning | Cost plan | PM/Finance | WBS | Budget, EAC inputs | Cost baseline | budget, contingency | At gate: approved | No | EAC engine | Cost event | Over EAC | Sponsor | Cost |
| Planning | Change process | PM | Governance | Define workflow | Change plan | thresholds | process exists | No | change_engine | CR logged | Pending | Board | Change |
| Gate 04 | Plan approval | Governance | IMP | APPROVE PLAN | PROJECT_BASELINE | plan checks.* | All pass | **Yes** | Baseline snapshot | Submit | Incomplete | Sponsor | Gate log |
| Mobilisation | Readiness checklist | PM | Baseline | Complete items | Mobilisation record | checklist | All recorded | No | Roll-up % | Item update | Blocker | Sponsor | Readiness |
| Gate 05 | Execution readiness | PM/Sponsor | Scores | READY/NOT READY | Decision | dimension % | READY or override | **Yes** | Block execute | Complete mob | NOT READY | Executive | Readiness |

### 7.4 Control engines (lifecycle-spanning)

| Stage | Process | PM Role | Input | Activity | Output | Required Data | Validation | Gate | Automation | Trigger | Alert | Escalation | Report |
|-------|---------|---------|-------|----------|--------|---------------|------------|------|------------|---------|-------|------------|--------|
| Control | Risk | PM/Owner | Event | Assess/respond | Response | prob/impact/exposure | Exposure calc | No | Auto-monitor | Trigger fired | Threshold | Sponsor | PCR §06 |
| Control | Issue | PM | Issue | Resolve→verify→close | Closed | verification | RESOLVED≠CLOSED | No | Overdue escalate | Due date | Overdue | Sponsor | PCR §07 |
| Control | Change | PM | Request | Impact→decide | Baseline update | 9 impact dims | Impact complete | **Yes** | Auto-impact | CR | Threshold | Governance | PCR §08 |
| Control | Decision | Owner | Need | Options→decide | Decision | deadline | On time | No | Deadline alerts | Approaching | Due soon | Authority | PCR §12 |
| Control | Dependency | PM | Status | Propagate delay | Forecast | source, activity | Cascade run | No | dependency_engine | Delay | Milestone hit | Owner chain | PCR §11 |
| Control | Schedule intel | PM | Actuals | Float, forecast | Early warning | baseline/actual | Float rules | No | 80% float msg | Slippage | Float | PM | PCR §04 |
| Control | Cost intel | PM/Finance | Actuals | EAC, variance | Forecast | commitments | EAC | No | Variance | Threshold | > budget % | Sponsor | PCR §09 |
| Control | Attention queue | System | Signals | Prioritise | CRITICAL/HIGH/… | priority rules | Engine rules | No | Continuous | Any threshold | Digest | Per item | PCR §01 |

### 7.5 Closeout & platform

| Stage | Process | PM Role | Input | Activity | Output | Required Data | Validation | Gate | Automation | Trigger | Alert | Escalation | Report |
|-------|---------|---------|-------|----------|--------|---------------|------------|------|------------|---------|-------|------------|--------|
| Commissioning | Checklist | PM/Tech | Build | Test, train, ops | Record | checklist | Critical done | No | Defect link | Test | Critical defect | Sponsor | Commissioning |
| Acceptance | Gate | PM/Business | Deliverables | ACCEPT/REMEDIATE | Acceptance | criteria | Met | **Yes** | Checklist | Delivery | Fail test | Sponsor | Acceptance |
| Handover | Transition | PM/Ops | Accepted | SLA, docs, assets | TRANSITION_COMPLETE | handover list | Ownership | No | Outstanding log | Milestone | No SLA | BO | Handover |
| Closure | Close | PM/Sponsor | Handover | Checklist + sign-off | CLOSED | closure.* | Sponsor OK | **Yes** | Archive | Handover done | Open contract | Sponsor | Final |
| Benefits | Realisation | Benefits owner | BC benefits | Measure actual | % realisation | target, actual | Variance narrative | No | Prompt "why?" | Period | Under target | Sponsor | Benefits |
| Platform | can_advance | System | Signals | Enforce rules | Allow/block | advancement.yaml | All rules | **Yes** | advancement svc | Advance | Reasons | Override | Audit |
| Platform | Override | Executive | Block | Reason+impact | Audit+move | override fields | Never silent | **Yes** | Audit event | Request | Compliance | Audit | Audit trail |

---

## 8. Project Control Report (single data model)

Generated by **reporting_engine** ([`engines/control_pipeline.yaml`](./engines/control_pipeline.yaml)):

1. Executive Summary  
2. Project Health  
3. Progress  
4. Schedule  
5. Milestones  
6. Risks & Impact Intelligence  
7. Issues & Resolution  
8. Changes  
9. Cost  
10. Deliverables & Quality  
11. Dependencies  
12. Decisions  
13. Actions  
14. Stakeholders  
15. Procurement/Vendors  
16. Forward Look  
17. Escalations  

---

## 9. Audit trail

Every material action: **WHO, WHAT, WHEN, BEFORE, AFTER, WHY, APPROVAL** — immutable. Schema: [`audit/event_schema.yaml`](./audit/event_schema.yaml).

---

## 10. Next implementation steps

1. Import `workflow_processes.yaml` into issue tracker (one ticket per `id`).  
2. Derive **entity-relationship model** from `data_relationships.yaml` + stage mandatory artifacts.  
3. Implement **advancement service** first (gates + override + audit).  
4. Implement **event bus** from `event_catalog.yaml` with one golden path: `VENDOR_MISSED_DELIVERY`.  
5. Build **PCR** as read-model projections over the same graph.  

---

## 11. Document map

| Artifact | Role |
|----------|------|
| `PROJECT_WORKFLOW_BIBLE.md` (this file) | Human-readable bible + summary matrices |
| `workflow_processes.yaml` | Complete process register with IDs |
| `project_lifecycle.yaml` | Stage order, gates, transitions |
| `stages/lifecycle_stages.yaml` | Entry/exit and artifacts per stage |
| `cross_cutting_domains.yaml` | Non-sequential domains and pipelines |
| `rules/advancement.yaml` | No-skipping and override |
| `data_relationships.yaml` | Canonical graph |
| `engines/control_pipeline.yaml` | Control, priority, reporting |
| `events/event_catalog.yaml` | Event-driven automation |
| `audit/event_schema.yaml` | Enterprise audit |
