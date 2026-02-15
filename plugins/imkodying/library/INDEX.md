# Library Index

Reference documents for all agents. Documents are organized into three tiers that determine when and whether to load them.

---

## How to Use This Index

**Step 1:** Load all Tier 1 documents — they apply to every agent on every task.

**Step 2:** Check your role against the Tier 2 table. Load documents where your role appears under `relevant_agents`.

**Step 3:** For each Tier 3 document, evaluate the `load_when` conditions against your current task. Load it only if at least one condition is a **clear, unambiguous match**. Do not load speculatively — "might be relevant" is not sufficient.

Evaluating conditions: read each `load_when` bullet and ask "Does this describe what I am doing right now?" If yes: load it. If uncertain or no: skip it.

---

## Tier 1 — Universal

Load these regardless of role, task type, or domain. No condition to evaluate.

| Document | Summary |
|----------|---------|
| [writing-quality.md](./writing-quality.md) | Pyramid principle, BLUF, precision, active voice, making recommendations, formatting for scannability. Applies to all written output. |

---

## Tier 2 — Role-Specific

Load based on your agent role. Check the `relevant_agents` column — if your role is listed, load this document.

| Document | Relevant Agents | Summary |
|----------|-----------------|---------|
| [planning-frameworks.md](./planning-frameworks.md) | **planner**, orchestrator | WBS, dependency mapping, critical path, MoSCoW/RICE prioritization, risk matrices, milestone definition, success criteria. |
| [research-methods.md](./research-methods.md) | **researcher**, reviewer | Primary vs secondary sources, triangulation, competitive analysis, source evaluation, constraint identification, edge case surfacing, knowledge gaps. |
| [implementation-patterns.md](./implementation-patterns.md) | **implementer**, reviewer | SOLID principles, design patterns (Factory, Strategy, Observer, Repository), error handling, API design, configuration management, defensive programming. |
| [testing-strategies.md](./testing-strategies.md) | **tester**, implementer, reviewer | Testing pyramid, TDD/BDD, property-based testing, full edge case taxonomy, AAA test structure, acceptance criteria framework. |
| [synthesis-techniques.md](./synthesis-techniques.md) | **orchestrator**, reviewer | Dialectical synthesis, MECE structuring, Borda count voting, conflict resolution, signal vs noise in reviews, synthesis anti-patterns. |

---

## Tier 3 — Domain & Language Specific

Load **only** when a `load_when` condition clearly matches your current task. Evaluate each document independently. Loading an irrelevant Tier 3 document wastes context and adds noise.

**Decision rule:** if you could complete your task well without this document, skip it.

| Document | Load When | Never Load When | Relevant Agents |
|----------|-----------|----------------|-----------------|
| [cpp-style-guidelines.md](./cpp-style-guidelines.md) | Working with C++ code (.cpp, .h, .hpp, .cc); reviewing a C++ implementation; writing C++ style feedback | Task does not involve C++ code | implementer, reviewer |

---

## Adding Documents to This Index

When adding a new library document:

1. Choose the correct tier:
   - **Tier 1**: applies to all agents on all tasks (very rare — only `writing-quality.md` qualifies currently)
   - **Tier 2**: always useful for one or more specific agent roles
   - **Tier 3**: only useful when a specific domain, language, framework, or condition applies

2. Set frontmatter fields:
   - `relevance_scope`: `universal` / `role-specific` / `domain-specific` / `language-specific`
   - `load_when`: list of evaluatable conditions (specific enough that an agent can make a binary yes/no decision)
   - `never_load_when`: list of explicit exclusion conditions
   - `relevant_agents`: which agents should ever load this
   - `topics`, `summary`, `key_concepts`, `related_documents`

3. Add a row to the correct tier table above.

4. **Tier 3 `load_when` conditions must be specific.** Bad: "when working on software." Good: "when writing or reviewing C++ code (.cpp, .h, .hpp files)." An agent must be able to evaluate the condition without ambiguity.
