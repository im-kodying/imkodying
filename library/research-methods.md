---
title: Research Methods & Knowledge Gathering
id: research-methods
version: 1.0.0
topics:
  - research
  - knowledge-gathering
  - prior-art
  - source-evaluation
  - competitive-analysis
  - literature-review
  - constraints
  - edge-cases
  - context
relevant_agents:
  - researcher
  - reviewer
relevant_task_types:
  - technology-selection
  - system-design
  - problem-investigation
  - debugging
  - competitive-analysis
  - any-task-requiring-background-knowledge
summary: |
  Methods for effective research including primary vs secondary sources,
  triangulation, competitive analysis, source evaluation, surfacing
  constraints and non-obvious edge cases, and organizing findings.
relevance_scope: role-specific
load_when:
  - "selecting a technology, library, or approach"
  - "investigating an unknown problem space"
  - "evaluating existing solutions or prior art"
  - "gathering background context before implementation"
  - "identifying constraints or edge cases through research"
never_load_when:
  - "task does not involve research, technology evaluation, or knowledge gathering"
key_concepts:
  - Primary vs secondary sources
  - Source triangulation
  - Competitive analysis
  - Literature review
  - Constraint identification
  - Edge case surfacing
  - Knowledge gaps
  - Confirmation bias
related_documents:
  - planning-frameworks.md
  - synthesis-techniques.md
---

# Research Methods & Knowledge Gathering

---

## Research Scope and Strategy

### Before You Search: Define the Questions

Effective research starts by stating what you need to know, not what you want to confirm. Write down:

1. **Core question**: What is the primary thing I need to understand?
2. **Decision questions**: What decisions does this research need to inform?
3. **Constraint questions**: What constraints or limitations should I expect?
4. **Failure questions**: How has this approach failed for others?

Answering your own questions before searching reveals your prior beliefs — which is where confirmation bias lives.

### Research Breadth vs Depth

Start broad, then narrow:
1. **Orientation pass**: 5–10 minutes of broad reading to understand the landscape
2. **Depth pass**: focused investigation of the 2–3 most relevant areas
3. **Edge case pass**: specifically searching for failure cases, gotchas, and minority opinions

Do not skip the orientation pass. Starting deep leads to missing adjacent solutions.

---

## Source Types and Evaluation

### Primary Sources (highest credibility)
- Original documentation (official docs, RFCs, specifications)
- Peer-reviewed research and academic papers
- Primary benchmarks and empirical measurements
- Changelogs and release notes
- Source code

### Secondary Sources (use with verification)
- Technical blog posts and tutorials
- Conference talks and presentations
- Stack Overflow answers (check date and vote count)
- Comparison articles (check for sponsorship or conflicts of interest)

### Tertiary Sources (low credibility, use for orientation only)
- AI-generated summaries
- Wikipedia
- Marketing materials
- "Top 10" listicles

### Source Evaluation Checklist

For any source, ask:
- **Recency**: is this still accurate? Technology moves fast. A 3-year-old tutorial may describe a deprecated API.
- **Authority**: does the author have direct experience with this? Are they citing primary sources?
- **Motive**: does the author have a financial or reputational interest in a particular answer?
- **Specificity**: are claims specific and verifiable, or vague and hedged?
- **Conflict**: does this contradict other reputable sources? If so, why?

---

## Triangulation

A single source is not evidence. Triangulate every significant claim across at least two independent sources.

### When Sources Disagree

Do not simply pick the one that confirms your prior belief. Instead:
1. Identify why they disagree (different versions? different use cases? different definitions?)
2. Seek a third source that can adjudicate
3. If the disagreement cannot be resolved, surface it explicitly in your output: "Sources disagree on X. [Source A] says Y because [reason]. [Source B] says Z because [reason]. The resolution depends on [context]."

---

## Competitive Analysis

### Mapping the Landscape

When evaluating approaches, tools, or technologies:

1. **Identify all known options** — do not evaluate until you have a complete list
2. **Group by approach** — options that solve the problem the same way can be evaluated together
3. **Define evaluation criteria** — what matters for this specific use case? (performance, maturity, license, ecosystem, learning curve, etc.)
4. **Score against criteria** — qualitatively or quantitatively
5. **Identify the tradeoff** — there is almost never a dominant option on all criteria. Name the tradeoff explicitly.

### Evaluation Dimensions (customize per task)
- **Maturity**: production-proven vs experimental
- **Ecosystem**: community size, library support, job market
- **Performance**: benchmarks under conditions matching your use case
- **Operational complexity**: how hard to run in production
- **License**: open source, commercial, copyleft restrictions
- **Maintenance**: actively maintained vs abandoned
- **Migration cost**: how hard to switch away later

### Failure Mode Research

For every option under consideration, specifically search for failure reports:
- GitHub issues labeled "bug" or "question" for open-source projects
- Post-mortems and incident reports mentioning the technology
- "X problems" or "X gotchas" or "migrating away from X"

Failure mode research is the most valuable research most people skip.

---

## Surfacing Constraints and Edge Cases

### Constraint Categories

| Type | Questions to Ask |
|------|-----------------|
| **Technical** | What is the performance ceiling? What breaks at scale? What assumptions does this make about the environment? |
| **Operational** | How is this deployed and monitored? What fails silently? |
| **Regulatory/Legal** | What data handling requirements apply? What licenses are involved? |
| **Organizational** | What skills does this require? What is the maintenance burden? |
| **Time** | What are the rate limits, timeouts, and latency characteristics? |

### Edge Case Taxonomy

When identifying edge cases, work through these categories systematically:

**Boundary conditions:**
- Empty input (null, empty string, empty list, zero)
- Single-element input where multiple are expected
- Maximum size / limit exceeded
- Minimum below threshold

**Type and format:**
- Wrong type (string where int expected)
- Encoding issues (UTF-8, Unicode edge cases)
- Locale-dependent behavior (decimal separators, date formats)
- Leading/trailing whitespace, invisible characters

**Timing and concurrency:**
- Race conditions when multiple actors access shared state
- Timeout behavior
- What happens if the operation is interrupted mid-way?
- Clock skew in distributed systems

**Failure and degradation:**
- What if a dependency is unavailable?
- Partial failures (some records succeed, some fail)
- Retry behavior and idempotency
- What does failure look like from the caller's perspective?

---

## Organizing and Presenting Research

### The Inverted Pyramid

Structure research output with the most important finding first:
1. **Core finding**: the single most important thing the reader needs to know
2. **Supporting evidence**: 2–4 pieces of evidence that support it
3. **Nuance and exceptions**: where the core finding does not hold
4. **References**: sources for all significant claims

### Distinguishing Knowledge Types

Label your claims:
- **Established fact**: widely documented, multiple sources, stable
- **Best practice**: community consensus, may have dissenting views
- **My assessment**: your interpretation of available evidence
- **Uncertain**: insufficient evidence to conclude, or sources conflict
- **Assumption**: taken as true for this analysis, not verified

Never mix these without distinguishing them. An "established fact" that is actually an assumption is dangerous.

### Identifying Knowledge Gaps

The most honest and useful part of a research output is the section on what is unknown. For each knowledge gap:
- State what is unknown
- State why it matters (what decision it affects)
- State what would be needed to resolve it (specific search, experiment, consultation)
