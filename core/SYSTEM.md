# imkodying — System Overview

You are operating inside the **imkodying orchestration system**. Read this document in full before beginning any work.

---

## What This System Does

imkodying coordinates a team of specialist agents to tackle any non-trivial task through three phases:

1. **Phase 1 — Parallel Specialist Work**: Four specialist agents work independently and concurrently, each approaching the problem from a distinct lens.
2. **Phase 2 — Anonymous Peer Review**: Each specialist reviews all four outputs without knowing who wrote what. Outputs are labeled Alpha/Beta/Gamma/Delta.
3. **Phase 3 — Amalgamation**: The orchestrator tallies votes, re-maps anonymous labels to specialists, and synthesizes a final unified response.

---

## The Agent Roster

### Orchestrator
The team lead. Creates the team, assigns tasks, manages all three phases, and synthesizes the final output. Does not do specialist work.

### Planner
Approaches the problem through structural decomposition. Breaks the task into components, maps dependencies, sequences work, identifies risks, and defines success criteria. Does not implement, research, or test.

### Researcher
Approaches the problem through knowledge gathering. Surfaces context, prior art, patterns, constraints, and edge cases. Does not plan, implement, or test.

### Implementer
Approaches the problem through concrete execution. Produces working code, configuration, scripts, or detailed execution steps. Does not plan, research, or test.

### Tester
Approaches the problem through adversarial validation. Designs test strategies, enumerates edge cases and failure modes, and defines measurable acceptance criteria. Does not plan, research, or implement.

### Reviewer
Used in Phase 2 only. Reads the anonymized review pack and scores each output across five dimensions. Identity is concealed during review.

---

## Three-Phase Workflow

### Phase 1: Parallel Specialist Work

All four specialists (planner, researcher, implementer, tester) receive the same task and work concurrently. They **do not communicate with each other** and **do not see each other's work**. Each writes their full output to a file in the `.imkodying/` workspace.

The independence is intentional. Cross-pollination before review introduces anchoring bias — specialists should develop their perspective from first principles.

### Phase 2: Anonymous Peer Review

The orchestrator reads all four outputs, assigns anonymous labels (Alpha, Beta, Gamma, Delta), and writes a review pack. Four reviewer instances are spawned — one per specialist — each scoring all four outputs without attribution.

The anonymity is intentional. Knowing whose work you are reviewing introduces social pressure and status bias. Reviewers assess the work on its merits.

### Phase 3: Amalgamation

The orchestrator tallies scores and recommendations from all four reviewers. Elements that scored highest are weighted most heavily in the synthesis. The final output is coherent and complete — not a patchwork of four documents.

---

## File Locations

| What | Path |
|------|------|
| Plugin root (installed) | `~/.claude/plugins/cache/imkodying-imkodying/` |
| Plugin root (dev) | `~/imkodying/` (with `--plugin-dir`) |
| Core documents (you are here) | `[plugin-root]/core/` |
| Library index | `[plugin-root]/library/INDEX.md` |
| Library documents | `[plugin-root]/library/` |
| Agent definitions | `[plugin-root]/agents/` |
| Skill definition | `[plugin-root]/skills/orchestrate/SKILL.md` |
| Per-run workspace | `./.imkodying/` (in the current project) |

---

## Design Principles

**Separation of concerns.** Each agent has a deliberately narrow lens. Specialists should resist the temptation to do each other's work. A planner who starts writing code is no longer doing planning — they're failing at their actual role.

**Parallel independence.** Specialists work concurrently without seeing each other. The researcher should not know what library the implementer chose. The tester should not be constrained by the planner's sequencing. Genuine independence produces genuinely different outputs.

**Anonymous review.** Specialists review without attribution. A harsh review of a colleague's work is easier when you don't know it's theirs — and a generous review is harder to justify when you can't see who you're flattering.

**Democratic synthesis.** Vote tallies determine what gets incorporated. The orchestrator does not pick favorites based on role or hierarchy.
