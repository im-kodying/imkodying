# imkodying — Output Standards

Read this document in full before beginning any work. Your output will be reviewed and scored against these criteria.

---

## Why This Matters

Your output is not the final product — it is an input to a synthesis process. Reviewers will score it blindly. High-scoring elements get incorporated into the final response. Low-scoring elements get cut or corrected. The quality of the final synthesis depends entirely on the quality of what each specialist contributes.

Write as if you are presenting to a skeptical expert who has no prior context and will judge your work on its own merits.

---

## The Scoring Rubric

Reviewers score every output on five dimensions. Understand these before writing.

### Correctness (30%)
Is the content accurate? Does it actually address the problem as stated? Are claims factually sound? Is the reasoning logically valid?

**What fails this criterion:**
- Factual errors or unsupported assertions
- Addressing the wrong problem or a misinterpretation of the task
- Logical gaps or contradictions
- Recommendations that would not work in practice

**What passes this criterion:**
- Claims that are accurate and defensible
- Reasoning that holds up to scrutiny
- Recommendations that would actually work

### Completeness (25%)
Does the output cover what it should for its role? Are significant gaps present? Does it address the full scope of the task or only the easy parts?

**What fails this criterion:**
- Missing entire sections that the role demands
- Handling only the happy path with no mention of complications
- Vague coverage of hard parts ("this would need to be figured out")

**What passes this criterion:**
- Full coverage of the role's responsibilities
- Hard parts addressed explicitly, even if not fully resolved
- Acknowledgment of what is out of scope with a rationale

### Clarity (20%)
Is the output well-organized and easy to understand? Can a skilled reader follow it quickly? Is the writing precise?

**What fails this criterion:**
- Poor organization with no discernible structure
- Ambiguous language that could be interpreted multiple ways
- Jargon without definition in context that warrants it
- Dense walls of text with no visual hierarchy

**What passes this criterion:**
- Clear section headers that signal the content
- Concise, precise writing — one meaning per sentence
- Code and other structured content properly formatted
- Logical flow from beginning to end

### Actionability (15%)
Can someone act on this? Is it concrete and specific, or vague and theoretical? Does it tell the reader what to do, not just what to think about?

**What fails this criterion:**
- Abstract recommendations with no concrete steps
- "It depends" answers without guidance on how to decide
- Long lists of options with no recommendation
- Outputs that describe the problem without prescribing a solution

**What passes this criterion:**
- Specific, executable recommendations
- Concrete examples, code, or step-by-step instructions
- When multiple options exist, a clear recommendation with rationale
- A reader could take action immediately after reading

### Originality (10%)
Does the output offer a non-obvious insight or approach? Does it contribute something that a generic response would miss? Does it reflect genuine thinking about this specific problem?

**What fails this criterion:**
- Generic boilerplate that could apply to any task
- Only stating what is already obvious from the task description
- Repeating conventional wisdom without applying it to this context

**What passes this criterion:**
- Insights specific to this task that required actual analysis
- Surfacing non-obvious constraints, risks, or patterns
- A perspective that genuinely adds something beyond the expected

---

## Formatting Standards

### Structure
- Use clear markdown headers (`##`, `###`) to organize content
- Every major section should have a header
- Bullet lists for enumerated items; prose for explanation
- Code blocks with language identifiers for all code: ` ```python `, ` ```bash `, etc.

### Length
- As long as the content demands; no longer
- Thoroughness is valued, but padding is not
- If a section genuinely has nothing to say, say so in one sentence and move on
- Do not add sections to look comprehensive if they are empty

### Language
- Write in active voice: "the system validates input" not "input is validated"
- Be specific: "3 API calls per second" not "frequent calls"
- Flag uncertainty explicitly: "this assumes X — if X is false, the approach changes"
- Do not hedge everything: take a position and defend it

### Assumptions
- State every significant assumption at the point where it affects your output
- Do not bury assumptions — surface them clearly
- Format: "**Assumption:** [statement]. If this is wrong, [consequence]."

---

## What Good Looks Like

A high-scoring output:
1. Correctly addresses the actual task (not a related easier task)
2. Covers all sections expected for its role without gaps
3. Is organized so a reader can find what they need in under 30 seconds
4. Gives concrete, specific recommendations — not "it depends"
5. Contains at least one insight that is non-obvious and specific to this problem
6. Flags all assumptions explicitly
7. Uses proper code formatting for any code

A low-scoring output:
1. Misses the point of the task
2. Is incomplete — entire sections are vague or absent
3. Is hard to navigate — no clear structure
4. Lists options without recommending one
5. Contains only generic advice that applies to any task
6. Makes hidden assumptions that, if wrong, invalidate the output
7. Has poorly formatted or non-runnable code
