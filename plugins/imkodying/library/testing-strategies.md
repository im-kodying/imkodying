---
title: Testing Strategies & Validation Patterns
id: testing-strategies
version: 1.0.0
topics:
  - testing
  - validation
  - quality-assurance
  - tdd
  - bdd
  - property-based-testing
  - edge-cases
  - test-design
  - acceptance-criteria
  - test-coverage
relevant_agents:
  - tester
  - implementer
  - reviewer
relevant_task_types:
  - software-development
  - code-review
  - system-validation
  - debugging
  - quality-assessment
summary: |
  Comprehensive testing strategies including the testing pyramid, TDD/BDD cycles,
  property-based testing, edge case taxonomy, test design patterns, and practical
  frameworks for defining measurable acceptance criteria.
relevance_scope: role-specific
load_when:
  - "designing a test strategy for a software component"
  - "writing or reviewing test cases"
  - "evaluating test coverage or quality"
  - "identifying edge cases and failure modes"
  - "defining measurable acceptance criteria"
never_load_when:
  - "task does not involve testing, validation, or quality assessment"
key_concepts:
  - Testing pyramid
  - TDD (Red-Green-Refactor)
  - BDD (Given-When-Then)
  - Property-based testing
  - Edge case taxonomy
  - Boundary value analysis
  - Equivalence partitioning
  - Mutation testing
  - Contract testing
related_documents:
  - implementation-patterns.md
  - planning-frameworks.md
---

# Testing Strategies & Validation Patterns

---

## The Testing Pyramid

The testing pyramid describes the recommended distribution of test types:

```
        /\
       /  \   ← E2E / UI Tests (few, slow, brittle, high confidence)
      /────\
     /      \ ← Integration Tests (moderate number, medium speed)
    /────────\
   /          \ ← Unit Tests (many, fast, focused, easy to maintain)
  /────────────\
```

**Unit tests:** Test a single unit of behavior in isolation. Fast to run (<1ms each). Cover the majority of logic. Mock or stub external dependencies.

**Integration tests:** Test the interaction between two or more components (e.g., code + database, code + external API). Slower. Cover the boundaries between systems.

**End-to-end tests:** Test the full system from the user's perspective. Slowest. Most brittle (break for reasons unrelated to bugs). Cover critical user paths only.

**Common mistake:** inverting the pyramid — having many E2E tests and few unit tests. E2E tests are expensive to write, slow to run, and hard to debug when they fail.

---

## Test-Driven Development (TDD)

### The Red-Green-Refactor Cycle

1. **Red**: write a failing test for the desired behavior (it must fail — if it passes, you're testing the wrong thing)
2. **Green**: write the minimum code to make the test pass (no more)
3. **Refactor**: improve the code without breaking the tests

### Benefits of TDD
- Forces you to think about the interface before the implementation
- Provides immediate feedback on correctness
- Produces a test suite that documents the behavior of the system

### When TDD Works Well
- Well-defined requirements with clear inputs/outputs
- Pure functions and business logic
- Anything that can be tested without I/O

### When TDD is Harder
- Exploratory work where requirements are unclear
- UI and rendering
- Integration with systems that are hard to mock

---

## Behavior-Driven Development (BDD)

### Given-When-Then Format

BDD scenarios describe behavior in business language:

```
Given [initial context / precondition]
When  [action or event occurs]
Then  [observable outcome]
```

**Example:**
```
Given a portfolio with $10,000 cash and no open positions
When a market buy order for 10 shares of AAPL at $150 is submitted
Then the order is filled
  And the portfolio cash balance is reduced by $1,500
  And the AAPL position size is 10 shares
```

### Why BDD Format
- Scenarios serve as living documentation
- Non-technical stakeholders can read and validate them
- Each scenario maps directly to a test case
- Forces explicit specification of preconditions

---

## Property-Based Testing

Instead of testing specific examples, property-based testing generates random inputs and verifies that properties always hold.

### Identifying Properties

**Inverse functions:**
```python
# encode then decode should give original value
assert decode(encode(x)) == x
```

**Idempotence:**
```python
# applying twice should give same result as once
assert normalize(normalize(x)) == normalize(x)
```

**Commutativity:**
```python
# order should not matter
assert merge(a, b) == merge(b, a)
```

**Invariant preservation:**
```python
# after any valid operation, invariants still hold
assert portfolio.total_value() >= 0
assert len(order_book.bids) > 0 implies order_book.bids[0].price >= order_book.bids[1].price
```

### Libraries
- Python: `hypothesis`
- JavaScript: `fast-check`
- Haskell: `QuickCheck` (the original)

---

## Edge Case Taxonomy

Use this taxonomy systematically when enumerating edge cases. Work through every category.

### Boundary Conditions
- Empty: null, empty string `""`, empty list `[]`, zero `0`
- Single element where multiple expected
- At exact limit (max - 0, max, max + 1)
- At exact minimum (min - 1, min, min + 1)
- Both limits simultaneously

### Data Type and Format
- Wrong type passed (string where int expected)
- Numeric overflow and underflow
- Floating point precision (`0.1 + 0.2 != 0.3`)
- Special float values: `NaN`, `Inf`, `-Inf`
- Encoding: UTF-8 multi-byte chars, null bytes, control characters
- Locale: decimal separator (`,` vs `.`), date format, timezone

### Collection and Structure
- Duplicate entries where uniqueness assumed
- Unsorted input where sorted assumed
- Circular references in graphs or trees
- Deeply nested structures (stack overflow potential)
- Very large collections (performance, memory)

### Timing and Concurrency
- Race condition: two actors modify same state simultaneously
- Timeout: operation takes longer than expected
- Clock skew: timestamps from different systems disagree
- Retry with non-idempotent operations
- Partial failure: N of M operations succeed

### External Dependencies
- Dependency unavailable (connection refused, DNS failure)
- Dependency returns unexpected status (503, malformed response)
- Dependency returns success but with empty or wrong data
- Rate limiting: too many requests
- Stale data: dependency returns old cached data

### Security / Adversarial
- SQL injection in string inputs
- Path traversal in file paths
- Integer overflow in calculations
- Extremely long inputs (DoS via regex backtracking, etc.)
- Inputs that look like code (script tags, format strings)

---

## Test Design Patterns

### Arrange-Act-Assert (AAA)

Structure every test in three sections:
```python
def test_order_fill_reduces_cash():
    # Arrange
    portfolio = Portfolio(cash=10_000)
    order = MarketOrder(symbol="AAPL", qty=10, price=150)

    # Act
    portfolio.fill(order)

    # Assert
    assert portfolio.cash == 8_500
```

### Test One Thing

Each test should have exactly one reason to fail. Multiple assertions in one test make failures ambiguous.

**Instead of:**
```python
def test_order_processing():
    fill(order)
    assert portfolio.cash == 8500       # could be either of these failing
    assert portfolio.positions["AAPL"] == 10
```

**Prefer:**
```python
def test_fill_reduces_cash(): ...
def test_fill_creates_position(): ...
```

### Parameterized Tests

When the same logic applies to multiple inputs, use parameterized tests rather than repeating test code:

```python
@pytest.mark.parametrize("qty, price, expected_cash", [
    (10, 150, 8_500),
    (1, 1000, 9_000),
    (100, 50, 5_000),
])
def test_fill_reduces_cash_by_notional(qty, price, expected_cash):
    portfolio = Portfolio(cash=10_000)
    portfolio.fill(MarketOrder(qty=qty, price=price))
    assert portfolio.cash == expected_cash
```

---

## Acceptance Criteria Framework

Acceptance criteria are the contract between what was asked for and what was built.

### INVEST Criteria for Test Cases
- **Independent**: can run in any order without affecting other tests
- **Valuable**: tests a behavior that matters to the system's correctness
- **Estimable**: scope is clear enough to estimate the effort to write it
- **Small**: tests one specific behavior, not a workflow
- **Testable**: can be automated and verified objectively

### Defining Done

Before writing a line of code, answer:
1. What inputs will the system accept?
2. What outputs will the system produce?
3. What invariants must always hold?
4. What are the performance criteria?
5. What does failure look like, and how is it communicated?

Acceptance criteria are not done until every question has a specific, verifiable answer.
