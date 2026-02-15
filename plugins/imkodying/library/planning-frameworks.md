---
title: Planning Frameworks & Decomposition Methods
id: planning-frameworks
version: 1.0.0
topics:
  - planning
  - decomposition
  - dependency-mapping
  - prioritization
  - risk-assessment
  - roadmapping
  - project-structure
  - milestones
  - work-breakdown
relevant_agents:
  - planner
  - orchestrator
relevant_task_types:
  - project-planning
  - system-design
  - feature-development
  - refactoring
  - any-non-trivial-task
summary: |
  Frameworks for decomposing problems, mapping dependencies, sequencing work,
  prioritizing components, and assessing risk. Covers WBS, dependency graphs,
  MoSCoW, RICE, critical path, and risk matrices.
relevance_scope: role-specific
load_when:
  - "decomposing a task into components or work packages"
  - "mapping dependencies between work items"
  - "sequencing or phasing a project"
  - "prioritizing requirements (MoSCoW, RICE)"
  - "assessing project risks"
  - "defining milestones or success criteria"
never_load_when:
  - "task does not involve project structure, sequencing, or planning"
key_concepts:
  - Work Breakdown Structure (WBS)
  - Dependency mapping
  - Critical path
  - MoSCoW prioritization
  - RICE scoring
  - Risk matrices
  - Milestone definition
  - Success criteria
related_documents:
  - synthesis-techniques.md
  - writing-quality.md
---

# Planning Frameworks & Decomposition Methods

---

## Work Breakdown Structure (WBS)

WBS decomposes a deliverable into progressively smaller components until each component is independently estimable and assignable.

### Rules for Good WBS
- **100% rule**: the WBS must include 100% of the work. Nothing is implied or assumed.
- **Mutually exclusive**: no overlap between work packages. Double-counting is a common failure.
- **Outcome-oriented**: items are deliverables, not activities. "Authentication module" not "write authentication code."
- **2-week rule**: any leaf node should be completable in 2 weeks or less. If not, decompose further.

### Structure
```
Level 0: Project
Level 1: Major deliverables (3–7)
Level 2: Components of each deliverable
Level 3: Work packages (leaf nodes — independently completable)
```

### Common Failure Modes
- Decomposing too early (Level 3 before Level 1 is stable)
- Missing infrastructure work (setup, deployment, monitoring, documentation)
- Forgetting integration work between components
- Treating activities as deliverables

---

## Dependency Mapping

### Types of Dependencies

| Type | Definition | Example |
|------|-----------|---------|
| **Finish-to-Start (FS)** | B cannot start until A is finished | Tests cannot run until code is written |
| **Start-to-Start (SS)** | B cannot start until A starts | Frontend and backend can start together |
| **Finish-to-Finish (FF)** | B cannot finish until A finishes | Docs must finish when code finishes |
| **External** | Depends on something outside the project | Waiting for API credentials from a vendor |

### Building a Dependency Graph
1. List all work packages from the WBS
2. For each package, ask: "What must exist before this can start?"
3. Draw directed edges from dependency → dependent
4. Identify the critical path (longest chain of dependencies)
5. Flag external dependencies — these are highest risk

### Critical Path
The critical path is the sequence of dependent tasks that determines the minimum project duration. Any delay on a critical-path task delays the project. Tasks off the critical path have float — they can slip without affecting the end date.

**Identifying the critical path:** Add durations to your dependency graph and find the longest path from start to end.

---

## Sequencing and Phasing

### Phase Design Principles
- **Early validation**: schedule work that validates key assumptions early. Don't build on unvalidated foundations.
- **Dependency ordering**: respect the dependency graph. Don't schedule B before A if B depends on A.
- **Risk-first**: if a component is high-risk, tackle it early when course correction is cheap.
- **Quick wins**: include early deliverables that provide value even if the project stops. Reduces stakeholder pressure.

### Milestone Definition
A milestone is a point in time, not a duration. Good milestones are:
- **Observable**: someone can verify it was reached without asking the team
- **Binary**: either achieved or not — no partial credit
- **Meaningful**: marks a real change in the project's state (a decision can be made, a handoff can occur)

Bad milestone: "Authentication is mostly done"
Good milestone: "User can log in with email/password and receive a JWT — verified by passing test suite"

---

## Prioritization Frameworks

### MoSCoW
Categorize every requirement:
- **Must have**: without this, the project fails. Non-negotiable.
- **Should have**: important, high value, but the project still delivers without it.
- **Could have**: nice to have. Include if time/resources allow.
- **Won't have (this time)**: explicitly out of scope for this cycle. Not forever — just not now.

**Use when:** you need to cut scope under time/resource constraints.
**Warning:** teams over-inflate "Must have." Challenge every Must.

### RICE Scoring
Score each item on four factors, then compute a priority score:

```
RICE Score = (Reach × Impact × Confidence) / Effort
```

| Factor | Definition | Scale |
|--------|-----------|-------|
| Reach | How many users/cases affected per period | Number |
| Impact | How much does it move the needle | 0.25 / 0.5 / 1 / 2 / 3 |
| Confidence | How sure are we about estimates | 50% / 80% / 100% |
| Effort | Person-weeks to complete | Number |

**Use when:** comparing many options with different profiles.
**Warning:** RICE can be gamed. Use it to inform judgment, not replace it.

---

## Risk Assessment

### Risk Matrix

Plot each risk on a 2×2:

```
         │ Low Impact  │ High Impact
─────────┼────────────┼────────────
High Prob│  Monitor   │  Mitigate
Low Prob │  Accept    │  Contingency
```

For each risk, record:
- **Description**: what could go wrong
- **Probability**: Low / Medium / High
- **Impact**: Low / Medium / High
- **Trigger**: what would cause this to occur
- **Mitigation**: action taken now to reduce probability or impact
- **Contingency**: action taken if the risk materializes

### Common Risk Categories
- **Technical**: unproven technology, performance unknowns, integration complexity
- **Dependency**: waiting on external teams, APIs, or vendors
- **Resource**: key person availability, skill gaps
- **Scope**: requirements changing, unclear specifications
- **Time**: deadline pressure forcing shortcuts

### Risk Prioritization
Focus mitigation effort on High Probability + High Impact risks. These are your "critical risks" — they should have explicit owners and active mitigation plans, not just contingency plans.

---

## Defining Success Criteria

Success criteria must be:
1. **Specific**: describes exactly what state must exist
2. **Measurable**: can be verified without subjective judgment
3. **Binary**: either met or not met — no partial credit
4. **Agreed**: all stakeholders align on the criteria before work begins

### Format
```
Given [precondition],
When [action or event],
Then [observable outcome].
```

**Example:**
> Given a user exists in the database with a valid email and password,
> When the user submits the login form with correct credentials,
> Then they receive a 200 response with a signed JWT and are redirected to the dashboard.

### Types of Success Criteria
- **Functional**: the system does X correctly
- **Performance**: the system does X within Y time/resource limits
- **Quality**: the code meets standard Z (test coverage, no critical linting errors)
- **User**: a user can accomplish task T without error
