---
title: Synthesis Techniques & Output Integration
id: synthesis-techniques
version: 1.0.0
topics:
  - synthesis
  - integration
  - combining-perspectives
  - voting
  - decision-making
  - conflict-resolution
  - amalgamation
  - structured-reasoning
  - weighing-evidence
relevant_agents:
  - orchestrator
  - reviewer
relevant_task_types:
  - synthesis
  - decision-making
  - multi-perspective-analysis
  - research-consolidation
  - design-review
summary: |
  Frameworks for synthesizing competing or complementary outputs into a unified
  result. Covers dialectical synthesis, MECE structuring, voting and scoring
  methods, conflict resolution, and evidence weighing. Core to the orchestrator's
  Phase 3 amalgamation task.
relevance_scope: role-specific
load_when:
  - "synthesizing multiple specialist outputs (orchestrator Phase 3)"
  - "resolving competing or conflicting recommendations"
  - "tallying and interpreting review votes"
  - "scoring and critiquing anonymous outputs as a reviewer"
  - "combining perspectives from multiple sources into a unified output"
never_load_when:
  - "task does not involve combining or synthesizing multiple viewpoints"
key_concepts:
  - Dialectical synthesis (thesis/antithesis/synthesis)
  - MECE structuring
  - Condorcet and Borda count voting
  - Conflict resolution
  - Evidence weighing
  - Complementary vs competing outputs
  - Signal vs noise in reviews
related_documents:
  - planning-frameworks.md
  - writing-quality.md
  - research-methods.md
---

# Synthesis Techniques & Output Integration

---

## Types of Multi-Perspective Disagreement

Before synthesizing, classify the relationship between specialist outputs:

| Relationship | Description | Synthesis Approach |
|-------------|-------------|-------------------|
| **Complementary** | Each covers different ground; together they're more complete | Merge all, organizing by topic |
| **Competing** | Multiple solutions to the same problem | Evaluate and select the better approach |
| **Contradictory** | Factual disagreement — cannot both be true | Resolve via evidence or flag as unresolved |
| **Redundant** | Same point made multiple times | Keep the clearest version; cut the rest |
| **Hierarchical** | One view is a special case of another | Incorporate the more general view as the frame |

---

## Dialectical Synthesis

Drawn from Hegelian dialectic: thesis + antithesis → synthesis.

**Process:**
1. Identify the **thesis** — the dominant or first-stated position
2. Identify the **antithesis** — the strongest opposing or complementary position
3. Produce a **synthesis** that:
   - Preserves what is correct in both
   - Resolves or transcends the contradiction
   - Is not merely a compromise (splitting the difference is not synthesis)

### Example

**Thesis (Planner):** "Build a custom order routing engine for maximum control."
**Antithesis (Researcher):** "Existing libraries (Nautilus Trader) already handle routing; building custom is high risk and high cost."
**Synthesis:** "Use Nautilus Trader as the execution engine. Build a thin custom layer above it for strategy-specific routing logic, contained in an adapter that can be replaced. Avoids the risk of building a routing engine while preserving future flexibility."

The synthesis is not "maybe build custom or maybe use a library" — it resolves the tension with a specific recommendation.

---

## MECE Structuring

MECE (Mutually Exclusive, Collectively Exhaustive) is a principle for organizing information so that:
- Every item fits in exactly one category (mutually exclusive — no overlap)
- All items together cover the full space (collectively exhaustive — no gaps)

### Applying MECE to Synthesis

When combining four specialist outputs:
1. Extract all claims, recommendations, and insights
2. Identify natural categories (e.g., "what to build," "how to build it," "how to validate it," "risks")
3. Assign each extracted item to exactly one category
4. Verify that the categories together cover the full problem
5. Write the synthesis section by section — each section corresponds to one category

**MECE fails when:**
- The same point appears in multiple sections (not mutually exclusive)
- An important aspect of the problem has no corresponding section (not exhaustive)

---

## Voting and Scoring Methods

### Tallying Reviewer Scores

When combining scores from multiple reviewers:

**Arithmetic mean:** simple average. Sensitive to outliers. One very high or very low score has disproportionate influence.

**Median:** middle value when sorted. Resistant to outliers. Better for small sample sizes (4 reviewers).

**Borda Count:** for ranking scenarios. Assign points by rank position (e.g., 4 points for 1st, 3 for 2nd, 2 for 3rd, 1 for 4th). Sum the points. Higher total = more preferred overall. Rewards consistent preference over being someone's top pick.

**For Phase 3 amalgamation:** use the recommendation counts (Include/Partial/Exclude) as the primary signal. Use the average score to break ties and determine emphasis.

### Interpreting Review Results

| Include | Partial | Exclude | Interpretation |
|---------|---------|---------|---------------|
| 4 | 0 | 0 | Strong consensus — include fully |
| 3 | 1 | 0 | Consensus with minor reservations — include, address reservations |
| 2 | 2 | 0 | Mixed — include the high-scoring elements, flag the contested aspects |
| 2 | 1 | 1 | Divided — examine what the excluders objected to |
| 0 | 0 | 4 | Consensus against — exclude or significantly revise |
| 1 | 0 | 3 | Strong consensus against — exclude unless the include voter identified something specific |

### Weighted Scoring

When reviewers disagree significantly, look at the substantive critiques to understand why. A score of 5 and a score of 1 for the same output might mean:
- The reviewers are applying different standards (resolve by checking rubric)
- The output is genuinely polarizing — strong in one dimension, weak in another
- One reviewer misread the output

Do not average conflicting scores without understanding the source of disagreement.

---

## Resolving Competing Recommendations

When specialists recommend different solutions to the same problem:

### Decision Criteria (in order)
1. **Correctness**: is one solution factually wrong or provably inferior?
2. **Evidence**: is one supported by stronger evidence in the research output?
3. **Consensus**: did reviewers score one consistently higher?
4. **Risk**: which has more acceptable failure modes?
5. **Reversibility**: which is easier to change later if wrong?

### If Genuinely Tied

Some decisions are legitimately judgment calls. When the evidence does not clearly favor one option:
1. State the tension explicitly: "Specialists disagreed on X. The planner recommends A because [reason]. The researcher recommends B because [reason]."
2. Make a recommendation based on the decision criteria above
3. Note what would change the recommendation: "If [condition], prefer B. Otherwise, prefer A."

Do not fake consensus where there is genuine uncertainty. Flagging it is more useful than hiding it.

---

## Signal vs Noise in Reviews

Not all reviewer critiques are equally valuable. Distinguish:

**High-signal critique:**
- Specific: "The dependency graph is missing the database migration step, which must precede the API deployment"
- Verifiable: points to something that can be checked
- Actionable: the synthesis can incorporate or address it

**Low-signal critique:**
- Vague: "This section could be more detailed"
- Subjective: "The tone is too informal"
- Unanswerable: "This doesn't consider all edge cases" (which ones?)

When synthesizing, prioritize high-signal critiques. Low-signal critiques may reflect reviewer preference rather than actual output deficiency.

---

## The Synthesis Output

A good synthesis is not a summary. It is a new, unified artifact that:
- Takes the best elements from each specialist contribution
- Resolves tensions between competing recommendations
- Addresses the most-flagged weaknesses
- Is organized by topic, not by which agent said what
- Stands alone — a reader who has not seen the specialist outputs can read and use it

### Synthesis Anti-Patterns

**The roundup:** "The planner said X. The researcher said Y. The implementer said Z." — This is not synthesis. It is delegation.

**The compromise:** "Given the disagreement, we'll do a bit of both." — Splitting the difference is usually wrong. Synthesize with conviction.

**The stack:** paste all four outputs end-to-end and call it done. The result has 4x the content and 0.25x the coherence.

**The autocrat:** ignore all but the highest-scored output. The point of multi-perspective work is integration, not selection.
