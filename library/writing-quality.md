---
title: Technical Writing Quality
id: writing-quality
version: 1.0.0
topics:
  - writing
  - communication
  - technical-documentation
  - clarity
  - structure
  - recommendations
  - precision
  - formatting
relevant_agents:
  - planner
  - researcher
  - implementer
  - tester
  - reviewer
  - orchestrator
relevant_task_types:
  - any-task-with-written-output
summary: |
  Principles and patterns for clear, precise, actionable technical writing.
  Covers structure (pyramid principle, BLUF), precision, active voice, formatting
  standards, making recommendations, and common failure modes to avoid.
relevance_scope: universal
load_when:
  - "always — applies to all agents on every task"
never_load_when:
  - "never — universally applicable regardless of domain, language, or task type"
key_concepts:
  - Pyramid principle
  - BLUF (Bottom Line Up Front)
  - Active vs passive voice
  - Precision and specificity
  - Recommendation writing
  - Formatting for scannability
  - Common writing anti-patterns
related_documents:
  - synthesis-techniques.md
---

# Technical Writing Quality

---

## Structure First

### The Pyramid Principle

Organize information so the most important point comes first, followed by supporting evidence, followed by detail.

```
┌─────────────────────────────────┐
│    Main point / recommendation  │  ← Lead with this
├─────────────────────────────────┤
│  Supporting argument 1          │  ← Then this
│  Supporting argument 2          │
│  Supporting argument 3          │
├─────────────────────────────────┤
│  Evidence and detail            │  ← Then this
│  Caveats and exceptions         │
└─────────────────────────────────┘
```

Most technical writing fails by inverting this: burying the conclusion at the bottom of a long preamble. The reader must read everything before knowing what you're recommending.

### BLUF (Bottom Line Up Front)

In every document, section, and paragraph: state your conclusion first, then explain why.

**Inverted (bad):**
> "We examined the latency characteristics of PostgreSQL, Redis, and SQLite under our expected load profile. Considering our requirements for ACID compliance, the need for complex queries, and the operational overhead of each system, we concluded that..."

**BLUF (good):**
> "Use PostgreSQL. It satisfies our ACID and query requirements with acceptable latency. Redis is not durable enough for trade records. SQLite lacks the concurrency model for multi-process writes."

### Section Headers

Headers should convey the content of the section — not just label it. A reader scanning headers should be able to reconstruct the argument without reading the body.

**Vague headers:** "Introduction," "Analysis," "Conclusions"
**Informative headers:** "Why PostgreSQL over Redis for Trade Storage," "Three Risks in the Current Architecture," "Step-by-Step Deployment Process"

---

## Precision and Specificity

The most common failure in technical writing is vagueness. Every vague statement is an opportunity to be specific.

### Replace Vague with Specific

| Vague | Specific |
|-------|---------|
| "Performance may be an issue" | "At 10,000 requests/second, this adds ~50ms latency per request" |
| "This is complex" | "This requires changes to 7 files across 3 modules" |
| "Handle errors appropriately" | "Return HTTP 400 for validation errors, 503 for upstream failures, log all 5xx with request context" |
| "Large amounts of data" | "Datasets exceeding 10GB" |
| "Recently" | "In the last 90 days" |
| "Many users" | "~40% of active users (based on July analytics)" |

### Quantify When Possible

Numbers anchor a reader's understanding in a way that adjectives cannot.

- Not "fast" but "< 10ms p95 latency"
- Not "large" but "50GB compressed"
- Not "often" but "in 3 of the last 5 production incidents"
- Not "most" but "78% of test cases"

If you do not have a number, explain why: "No benchmarks available for this configuration — this is an assumption."

---

## Active Voice

Active voice is clearer, shorter, and more direct than passive voice.

| Passive | Active |
|---------|--------|
| "The order was submitted by the strategy" | "The strategy submits the order" |
| "Errors are caught and logged by the middleware" | "The middleware catches and logs errors" |
| "The configuration is loaded at startup" | "The system loads configuration at startup" |

**When passive voice is appropriate:**
- When the actor is genuinely unknown: "The record was deleted at 14:32 UTC"
- When the actor is irrelevant and would distract: "The file is encoded in UTF-8"
- When emphasizing the recipient over the actor is correct

Default to active. Use passive deliberately.

---

## Making Recommendations

A recommendation without justification is an opinion. An opinion without a recommendation is useless. A recommendation requires:

1. **What to do** (specific, actionable)
2. **Why** (the reasoning that leads to this conclusion)
3. **Trade-offs acknowledged** (what is given up or risked)
4. **Conditions** (when this recommendation changes)

### Format
```
Recommendation: [specific action]

Rationale: [2–4 sentences of reasoning]

Trade-offs: [what you're accepting by choosing this]

Conditions: [what would change this recommendation]
```

### Hedging vs Uncertainty

There is a difference between appropriate acknowledgment of uncertainty and vague hedging:

**Appropriate uncertainty:**
> "Recommend PostgreSQL, with the caveat that if the team later needs geospatial queries at high volume, PostGIS performance should be benchmarked before committing."

**Vague hedging (unhelpful):**
> "PostgreSQL might be a good choice depending on your specific requirements and constraints."

The first is useful. The second tells the reader nothing they didn't already know.

---

## Formatting for Scannability

Technical readers scan before they read. Format so that scanning is productive.

### Use Tables for Comparisons

When comparing multiple options on multiple dimensions, a table conveys information more efficiently than prose.

```markdown
| Option | Latency | Durability | Operational Cost |
|--------|---------|-----------|-----------------|
| Redis  | < 1ms   | Configurable (AOF) | Low |
| PostgreSQL | 1–5ms | Full ACID | Medium |
| SQLite | < 1ms   | WAL mode | Very low, single writer |
```

### Use Bullet Lists Correctly

Bullet lists are for **enumerable items of equal weight**. They are not for everything.

**Use bullets for:**
- A list of steps (with numbers, not bullets, if order matters)
- A list of features or capabilities
- A list of risks, trade-offs, or constraints

**Do not use bullets for:**
- Flowing argument or reasoning (use prose)
- A single item
- Fragments that require the preceding prose to make sense

### Code Blocks

Always use fenced code blocks with language identifiers:

````markdown
```python
def calculate_pnl(trades: list[Trade]) -> Decimal:
    return sum(t.realized_pnl for t in trades)
```
````

Never put code inline when a block is appropriate. Never put code in a bullet list item without a block.

---

## Common Failure Modes

### The Preamble

Starting with context, background, and motivation before reaching the point. The reader must read several paragraphs before knowing what you're recommending.

**Fix:** Apply BLUF. State the conclusion first. Context goes after.

### The Laundry List

A list of items without prioritization, synthesis, or recommendation. Presents "all the options" without helping the reader decide.

**Fix:** Recommend. If you won't choose, explain what information is needed to choose.

### The Qualification Stack

Every statement is heavily qualified: "This could potentially be one approach that might work in some cases, depending on your specific situation."

**Fix:** State the direct claim, then note exceptions. "Use approach X. Exception: if Y, use Z instead."

### The False Balance

Treating all options as equally valid when evidence favors one. "Option A has these advantages; Option B has these advantages. It depends on your situation."

**Fix:** Weigh the evidence and state which is better for the common case.

### Missing Audience

Writing at the wrong abstraction level — too basic (insulting a technical reader) or too advanced (losing a non-specialist reader).

**Fix:** State your assumed audience at the top of long documents. Write for that audience consistently.

### Passive Observation

Describing what exists without stating what should be done about it. "The current implementation has a race condition" without recommending a fix.

**Fix:** For every problem identified, include a recommendation. Even if the fix is "this is acceptable given X" — that is a recommendation.
