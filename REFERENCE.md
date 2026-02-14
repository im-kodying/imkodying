# imkodying Plugin Reference

Multi-perspective agent orchestration with anonymous peer review and democratic synthesis.

---

## System Overview

Every task runs through three phases:

1. **Phase 1 — Parallel Specialist Work**: Four specialist agents tackle the problem concurrently, each working independently from their own perspective. They do not see each other's work.
2. **Phase 2 — Anonymous Peer Review**: Each specialist reviews all four outputs anonymously. Outputs are labeled Alpha/Beta/Gamma/Delta with no attribution. Reviewers score each and recommend Include, Partially Include, or Exclude.
3. **Phase 3 — Amalgamation**: The orchestrator tallies votes, re-maps labels to specialists, and synthesizes a final unified response that incorporates the highest-voted elements and addresses the most commonly-flagged weaknesses.

**Why this works:**
- **Parallel independence** prevents anchoring bias — each specialist develops their perspective without being influenced by the others
- **Anonymous review** eliminates social pressure and sycophancy — specialists don't know whose work they're critiquing
- **Democratic synthesis** weights the final output by vote, not by hierarchy — the best-reviewed elements get incorporated regardless of which agent produced them

---

## Agent Roster

---

### Orchestrator

**Invoked via:** `/imkodying:orchestrate [task]`
**Role:** Team lead and synthesizer

The Orchestrator does not do specialist work. It creates the agent team, assigns tasks, waits for phases to complete, manages the anonymization and review process, tallies votes, and synthesizes the final response. It is the only agent that sees all outputs and all reviews.

**Responsibilities:**
- Create workspace at `./.imkodying/`
- Spawn and coordinate the four specialist agents (Phase 1)
- Collect outputs and write the anonymized review pack (Phase 2 setup)
- Spawn and coordinate the four reviewer agents (Phase 2)
- Tally scores and re-map anonymous labels (Phase 3)
- Synthesize the final response and present vote summary (Phase 3)
- Clean up the agent team

**Output to user:** Final synthesis + vote summary table + perspective contributions breakdown

---

### Planner

**File:** `agents/planner.md`
**subagent_type:** `planner`
**Perspective:** Structural decomposition and roadmapping

The Planner approaches the problem as a project manager and systems thinker. It focuses on how to approach the problem, not on the details of execution. It produces structure, sequence, and feasibility analysis.

**Output sections:**
1. Problem decomposition — discrete, independently-completable components
2. Dependency map — what depends on what; what must come first
3. Sequenced roadmap — ordered phases with explicit milestones and deliverables
4. Risk assessment — blockers, unknowns, and assumptions with likelihood/impact
5. Resource considerations — skills, tools, data, infrastructure required
6. Success criteria — measurable, observable definition of done

**Does not:** Write code, gather background research, design tests, or produce any implementation artifact

---

### Researcher

**File:** `agents/researcher.md`
**subagent_type:** `researcher`
**Perspective:** Context, knowledge, and prior art

The Researcher approaches the problem as an investigator. It gathers everything that is already known — existing solutions, patterns, constraints, edge cases — and surfaces the knowledge the team needs before diving into execution. It thinks in terms of what's established, what's contested, and what's unknown.

**Output sections:**
1. Context & background — domain concepts, terminology, framing
2. Prior art & existing solutions — what has been tried, what worked, what didn't
3. Relevant patterns & best practices — standard approaches, field consensus, active debates
4. Constraints & edge cases — known limitations, gotchas, non-obvious failure modes
5. Key references — specific sources, tools, codebases worth consulting
6. Open questions — unknowns that need further investigation

**Does not:** Plan the work, write implementations, or design tests

---

### Implementer

**File:** `agents/implementer.md`
**subagent_type:** `implementer`
**Perspective:** Concrete execution and artifact creation

The Implementer approaches the problem as a builder. It produces the actual thing — working code, configuration, scripts, or executable step-by-step instructions. It prioritizes working and correct over elegant, and concrete over theoretical.

**Output sections:**
1. Core implementation — the primary artifact (code, config, etc.) in full
2. Setup & prerequisites — what must be in place before execution
3. Step-by-step execution — how to use the implementation from start to finish
4. Key technical decisions — choices made, alternatives rejected, tradeoffs accepted
5. Limitations & scope — what the implementation explicitly does not handle
6. Obvious extensions — 3–5 natural next steps

**Does not:** Create project plans, gather background research, or write tests

---

### Tester

**File:** `agents/tester.md`
**subagent_type:** `tester`
**Perspective:** Validation, quality, and adversarial thinking

The Tester approaches the problem as an adversary. Its job is to find what could go wrong and design a system to catch it. It thinks about boundary conditions, failure modes, and what "correct" actually means in measurable terms. It produces test artifacts that prove the solution works.

**Output sections:**
1. Test strategy — testing levels, philosophy, and overall approach
2. Test cases — enumerated scenarios with name, inputs, expected outputs, and category
3. Edge cases & failure modes — boundary conditions, adversarial inputs, concurrent/failure scenarios
4. Acceptance criteria — binary pass/fail criteria that define "complete and correct"
5. Test artifacts — actual test code or validation scripts
6. Risk areas — highest-risk parts of the solution, prioritized for testing attention

**Does not:** Plan the approach, research the domain, or build the solution

---

### Reviewer

**File:** `agents/reviewer.md`
**subagent_type:** `reviewer`
**Perspective:** Anonymous critical assessment

The Reviewer is used exclusively in Phase 2. It receives an anonymized review pack (outputs labeled Alpha/Beta/Gamma/Delta) and scores each against five weighted dimensions. It does not know which specialist produced which output, and its own identity as a specialist is concealed. Four reviewer instances run in parallel — one per specialist — each reviewing all four outputs independently.

**Scoring dimensions:**
| Dimension | Weight | What is assessed |
|-----------|--------|-----------------|
| Correctness | 30% | Accuracy, logical soundness, actually solves the problem |
| Completeness | 25% | Coverage, absence of significant gaps |
| Clarity | 20% | Organization, writing quality, ease of comprehension |
| Actionability | 15% | Concreteness, specificity, executability |
| Originality | 10% | Non-obvious insights, unique contributions |

**Output per reviewer:** Per-output score (1–5), strengths, weaknesses, recommendation (Include / Partially Include / Exclude), overall ranking, and a summary note on what's missing across all outputs.

**Anonymity design:** Outputs are relabeled by the orchestrator before being passed to reviewers. The mapping from labels to specialists is held only by the orchestrator and revealed only in the final Phase 3 output to the user.

---

## Workflow Diagram

```
User: /imkodying:orchestrate [task]
              │
              ▼
      ┌───────────────┐
      │  Orchestrator  │  Creates team, manages phases, synthesizes
      └───────┬───────┘
              │
              │  PHASE 1: Parallel Specialist Work
              ├────────────────────────────────────────────────┐
              │                                                │
    ┌─────────▼──────────────────────────────────────────┐    │
    │  planner    researcher    implementer    tester     │    │
    │    │            │              │            │       │    │
    │    ▼            ▼              ▼            ▼       │    │
    │  .imkodying/planner.md    .imkodying/implementer.md │    │
    │  .imkodying/researcher.md .imkodying/tester.md      │    │
    └────────────────────────────────────────────────────┘    │
              │                                                │
              │  Orchestrator reads all 4 outputs              │
              │  Assigns: Alpha / Beta / Gamma / Delta         │
              │  Writes: .imkodying/review-pack.md             │
              │                                                │
              │  PHASE 2: Anonymous Peer Review                │
    ┌─────────▼──────────────────────────────────────────┐    │
    │      reviewer×4  (R1, R2, R3, R4)                  │    │
    │   each reads review-pack.md, scores Alpha–Delta     │    │
    │   writes to .imkodying/review-R1.md … review-R4.md │    │
    └────────────────────────────────────────────────────┘    │
              │                                                │
              │  Orchestrator tallies votes                    │
              │  Re-maps Alpha/Beta/Gamma/Delta → specialists  │
              │                                                │
              │  PHASE 3: Amalgamation                         │
              ▼                                                │
      ┌───────────────┐                                        │
      │  Final Output  │ ◄──────────────────────────────────── ┘
      │  Synthesis     │
      │  Vote Summary  │
      │  Contributions │
      └───────────────┘
```

---

## Workspace Files

Each run creates temporary files in `./.imkodying/` within the current project directory. These are safe to delete after a session.

| File | Written by | Contents |
|------|-----------|----------|
| `planner.md` | planner | Full planner specialist output |
| `researcher.md` | researcher | Full researcher specialist output |
| `implementer.md` | implementer | Full implementer specialist output |
| `tester.md` | tester | Full tester specialist output |
| `review-pack.md` | orchestrator | Anonymized outputs labeled Alpha–Delta |
| `review-R1.md` | reviewer (R1) | Scores and critique from reviewer 1 |
| `review-R2.md` | reviewer (R2) | Scores and critique from reviewer 2 |
| `review-R3.md` | reviewer (R3) | Scores and critique from reviewer 3 |
| `review-R4.md` | reviewer (R4) | Scores and critique from reviewer 4 |

---

## Design Principles

**Separation of concerns.** Each specialist has a deliberately narrow lens. The planner doesn't implement; the implementer doesn't plan. This produces deeper specialist outputs than a generalist provides, and surfaces genuine disagreements between perspectives.

**Parallel independence.** Specialists work concurrently without seeing each other's work. This prevents anchoring — the researcher can't be nudged toward the implementer's chosen library, and the tester can't be biased by the planner's risk framing.

**Anonymous review.** Specialists don't know whose work they're reviewing, and their own identity is concealed. This eliminates social pressure and the tendency to be lenient on teammates or harsh on rivals. Reviews reflect genuine assessments.

**Democratic synthesis.** The orchestrator weights the final output by vote, not by role or hierarchy. An excellent tester output can outrank a mediocre planner output. The final synthesis is built from the best-reviewed elements across all perspectives, not from a predetermined structure.
