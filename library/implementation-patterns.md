---
title: Implementation Patterns & Code Quality
id: implementation-patterns
version: 1.0.0
topics:
  - implementation
  - code-quality
  - design-patterns
  - architecture
  - refactoring
  - solid-principles
  - clean-code
  - api-design
  - error-handling
  - configuration
relevant_agents:
  - implementer
  - tester
  - reviewer
relevant_task_types:
  - software-development
  - system-design
  - api-design
  - refactoring
  - code-review
  - debugging
summary: |
  Patterns and principles for writing high-quality, maintainable implementations.
  Covers SOLID principles, common design patterns, error handling, API design,
  configuration management, and practical code quality heuristics.
relevance_scope: role-specific
load_when:
  - "designing or writing a software implementation"
  - "choosing between architectural patterns or approaches"
  - "reviewing code for quality, correctness, or design"
  - "designing an API or module interface"
  - "refactoring existing code"
never_load_when:
  - "task does not involve software implementation or code"
key_concepts:
  - SOLID principles
  - Design patterns (Factory, Strategy, Observer, Repository)
  - Error handling
  - API design
  - Configuration management
  - DRY / YAGNI / KISS
  - Separation of concerns
  - Defensive programming
related_documents:
  - testing-strategies.md
  - writing-quality.md
---

# Implementation Patterns & Code Quality

---

## Core Principles

### SOLID

**Single Responsibility Principle (SRP)**
A class or module should have one reason to change. "Reason to change" = the stakeholder whose requirements drive its behavior. A class that handles both business logic and persistence has two reasons to change.

*Symptom it's violated:* you change one feature and something unrelated breaks.

**Open/Closed Principle (OCP)**
Open for extension, closed for modification. Add new behavior by adding new code, not by changing existing code.

*Implementation:* use abstraction (interfaces, base classes, higher-order functions) where behavior needs to vary. New behavior = new concrete implementation.

**Liskov Substitution Principle (LSP)**
A subtype must be substitutable for its parent without breaking the system. If you have to check what type something is before using it, LSP is likely violated.

*Practical rule:* if overriding a method requires you to weaken preconditions or strengthen postconditions, the inheritance is wrong.

**Interface Segregation Principle (ISP)**
Clients should not depend on interfaces they don't use. Many small, specific interfaces beat one large general-purpose interface.

*Symptom it's violated:* implementing a method by raising `NotImplemented` or passing.

**Dependency Inversion Principle (DIP)**
Depend on abstractions, not concretions. High-level modules should not depend on low-level modules; both should depend on abstractions.

*Implementation:* inject dependencies rather than instantiating them. The caller controls what implementation is used.

### DRY / YAGNI / KISS

**DRY (Don't Repeat Yourself):** Every piece of knowledge should have a single, unambiguous representation. Note: DRY is about *knowledge*, not *text*. Two similar-looking code blocks that implement different business rules are not a DRY violation.

**YAGNI (You Aren't Gonna Need It):** Do not build for requirements that do not yet exist. The cost of premature abstraction is higher than the cost of refactoring when the need actually arises.

**KISS (Keep It Simple, Stupid):** Prefer the simpler solution. Complexity compounds — every additional abstraction layer adds to the cognitive load of everyone who maintains the code.

---

## Design Patterns

### Factory
**When to use:** you need to create objects without specifying the exact class.
```python
class DataClientFactory:
    @staticmethod
    def create(provider: str) -> DataClient:
        match provider:
            case "ib": return IBDataClient()
            case "databento": return DatabentoClient()
            case _: raise ValueError(f"Unknown provider: {provider}")
```

### Strategy
**When to use:** a behavior needs to vary independently of the class that uses it.
```python
class OrderSizer:
    def __init__(self, strategy: SizingStrategy):
        self.strategy = strategy

    def size(self, signal: float, portfolio: Portfolio) -> int:
        return self.strategy.compute(signal, portfolio)
```

### Observer / Event Bus
**When to use:** you need loose coupling between components that react to events.
```python
class EventBus:
    def __init__(self):
        self._handlers: dict[str, list[Callable]] = {}

    def subscribe(self, event: str, handler: Callable):
        self._handlers.setdefault(event, []).append(handler)

    def publish(self, event: str, data: Any):
        for handler in self._handlers.get(event, []):
            handler(data)
```

### Repository
**When to use:** abstract data access from business logic.
```python
class TradeRepository(Protocol):
    def get(self, trade_id: str) -> Trade: ...
    def save(self, trade: Trade) -> None: ...
    def query(self, **filters) -> list[Trade]: ...
```

---

## Error Handling

### Principles

**Fail fast:** detect errors at the point they occur, not silently downstream. A wrong value that propagates far from its origin is much harder to debug than one caught at the source.

**Let it crash (where appropriate):** in systems with supervision/restart mechanisms, crashing on unexpected errors is often better than trying to recover from an unknown state. Prefer this in daemon/service code.

**Return errors as values:** in functions where failure is expected and handled, return errors rather than raising exceptions. Exceptions are for unexpected conditions.

```python
# Prefer for expected failures
def parse_config(path: str) -> Result[Config, str]:
    ...

# Prefer for unexpected conditions
def critical_operation(data: bytes) -> None:
    if not data:
        raise ValueError("critical_operation called with empty data — this is a bug")
```

**Classify errors:**
- **Transient**: retry may succeed (network timeout, rate limit)
- **Permanent**: retry will not help (invalid input, authentication failure)
- **Fatal**: the process cannot continue safely (corrupted state, resource exhaustion)

### Error Messages

A good error message:
1. States what went wrong (not just the exception type)
2. States what the system was trying to do
3. States what the caller can do about it (if anything)
4. Includes the relevant values (what was the input? what was expected?)

```python
# Bad
raise ValueError("invalid value")

# Good
raise ValueError(
    f"Order size {size} exceeds maximum {MAX_ORDER_SIZE} for instrument {instrument}. "
    "Reduce position size or split into multiple orders."
)
```

---

## API Design

### Principles for Clean APIs

**Make the common case easy, the hard case possible.** The simplest usage should require the minimum configuration. Advanced options should be available but not required.

**Fail early and loudly.** Validate inputs at the boundary. Do not accept invalid inputs and fail later in an opaque way.

**Be consistent.** Use the same patterns, naming conventions, and error types throughout. Inconsistency forces callers to remember special cases.

**Minimize surface area.** Expose what callers need. Unexposed internal details can change without breaking callers. The YAGNI principle applies to APIs too.

**Design for the caller, not the implementer.** The implementation should conform to what makes sense for callers, not the other way around.

### Naming

- Functions: verb + noun (e.g., `calculate_position`, `submit_order`, `fetch_bars`)
- Boolean parameters: avoid. Use enums or named arguments. `submit_order(side=Side.BUY)` not `submit_order(True)`.
- Avoid abbreviations in public APIs. `instrument` not `inst`. `quantity` not `qty` (unless `qty` is domain standard).

---

## Configuration Management

### Hierarchy of Configuration Sources (highest to lowest priority)

1. Environment variables (runtime overrides)
2. Config file (project-level settings)
3. Defaults (code-level fallbacks)

Always document: what can be configured, what the defaults are, and what environment variables control what.

### What Belongs in Config

- **In**: environment-specific values (URLs, credentials, feature flags, limits)
- **Not in**: business logic, algorithmic parameters that affect output correctness, hardcoded secrets

### 12-Factor Configuration

Never store credentials in code or version control. Use environment variables or a secrets manager. Validate that required configuration is present at startup, not at first use.

---

## Defensive Programming

**Validate at boundaries.** Validate external inputs (user input, API responses, file contents) at the point they enter your system. Trust internal code.

**Immutability by default.** Prefer immutable data structures where possible. Mutation is a frequent source of bugs in concurrent code and in long function chains.

**Explicit over implicit.** Avoid magic — implicit type coercion, implicit defaults that silently change behavior, implicit global state. When something is explicit, it can be read, tested, and debugged.

**Assert invariants.** Use assertions to document and enforce invariants that should always be true. They make bugs visible immediately rather than letting them propagate.

```python
assert 0 < position_size <= MAX_SIZE, f"Invariant violated: {position_size=}"
```
