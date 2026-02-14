---
title: C++ Style Guidelines & Modern C++ Patterns
id: cpp-style-guidelines
version: 1.0.0
topics:
  - cpp
  - c-plus-plus
  - style
  - modern-cpp
  - cpp17
  - cpp20
  - memory-management
  - raii
  - templates
  - performance
  - undefined-behavior
relevant_agents:
  - implementer
  - reviewer
relevant_task_types:
  - cpp-implementation
  - cpp-review
  - systems-programming
  - performance-critical-code
  - library-development
relevance_scope: language-specific
load_when:
  - "writing or reviewing C++ source files (.cpp, .cc, .cxx)"
  - "writing or reviewing C++ header files (.h, .hpp, .hxx)"
  - "reviewing a C++ implementation for style, correctness, or performance"
  - "writing C++ style feedback or critique"
  - "implementing a component that will be written in C++"
never_load_when:
  - "task does not involve C++ code — do not load for Python, Rust, Go, or other languages"
  - "task involves C but not C++"
summary: |
  Style guidelines and idiomatic patterns for modern C++ (C++17/20). Covers
  resource management (RAII, smart pointers), type safety, const correctness,
  naming conventions, template usage, undefined behavior avoidance, and
  performance-sensitive patterns.
key_concepts:
  - RAII (Resource Acquisition Is Initialization)
  - Smart pointers (unique_ptr, shared_ptr)
  - Move semantics
  - Const correctness
  - Prefer value semantics
  - Rule of Zero / Three / Five
  - Avoid raw pointers for ownership
  - Structured bindings (C++17)
  - std::optional, std::variant, std::expected
  - Concepts (C++20)
  - Undefined behavior avoidance
related_documents:
  - implementation-patterns.md
  - testing-strategies.md
---

# C++ Style Guidelines & Modern C++ Patterns

Target: C++17 minimum, C++20 preferred where available.

---

## Resource Management

### RAII — The Foundational Rule

Every resource (memory, file handle, lock, socket, database connection) must be tied to an object's lifetime. Acquire in the constructor; release in the destructor. Never manage resources manually.

```cpp
// Bad: manual management
FILE* f = fopen("data.bin", "rb");
// ... (can leak if exception thrown)
fclose(f);

// Good: RAII wrapper
{
    std::ifstream f("data.bin", std::ios::binary);
    // file closed automatically at scope exit, even on exception
}
```

### Smart Pointers — Ownership Policy

| Pointer Type | Use When |
|-------------|---------|
| `std::unique_ptr<T>` | Single owner. Default for heap allocation. Zero runtime cost over raw pointer. |
| `std::shared_ptr<T>` | Shared ownership. Use sparingly — reference counting has cost and can cause cycles. |
| `std::weak_ptr<T>` | Non-owning observer of a `shared_ptr`. Breaks cycles. |
| Raw pointer `T*` | Non-owning reference only. Never use to express ownership. |
| Raw reference `T&` | Non-owning, non-null reference. Preferred over raw pointer for non-owning references. |

```cpp
// Bad: raw pointer ownership
Widget* w = new Widget();
use(w);
delete w; // easy to forget, not exception-safe

// Good: unique_ptr ownership
auto w = std::make_unique<Widget>();
use(*w); // or use(w.get()) for legacy APIs
// automatically deleted
```

**Rule:** if you type `new` without immediately assigning to a smart pointer, stop and reconsider.

---

## The Rule of Zero / Three / Five

**Rule of Zero:** if your class manages no resources directly, define none of the special members. The compiler-generated versions are correct.

```cpp
struct Point {
    double x, y;
    // No destructor, copy, or move needed — compiler handles it
};
```

**Rule of Five:** if you define any of (destructor, copy constructor, copy assignment, move constructor, move assignment), define all five.

```cpp
class Buffer {
public:
    Buffer(size_t n) : data_(std::make_unique<char[]>(n)), size_(n) {}

    // Rule of Five — must define all if defining any
    ~Buffer() = default;
    Buffer(const Buffer& other) : data_(std::make_unique<char[]>(other.size_)), size_(other.size_) {
        std::copy(other.data_.get(), other.data_.get() + size_, data_.get());
    }
    Buffer& operator=(const Buffer&) = delete; // or implement fully
    Buffer(Buffer&&) noexcept = default;
    Buffer& operator=(Buffer&&) noexcept = default;

private:
    std::unique_ptr<char[]> data_;
    size_t size_;
};
```

---

## Type Safety

### Prefer `enum class` over `enum`

```cpp
// Bad: unscoped enum, implicit int conversion
enum Color { Red, Green, Blue };
int x = Red; // compiles — unintended

// Good: scoped enum, no implicit conversion
enum class Color { Red, Green, Blue };
Color c = Color::Red;
```

### `std::optional` for Nullable Values

```cpp
// Bad: sentinel values or output parameters
int find_index(const std::vector<int>& v, int target) {
    // returns -1 if not found — magic value
}

// Good: std::optional communicates intent
std::optional<size_t> find_index(const std::vector<int>& v, int target) {
    auto it = std::find(v.begin(), v.end(), target);
    if (it == v.end()) return std::nullopt;
    return std::distance(v.begin(), it);
}

if (auto idx = find_index(v, 42)) {
    use(*idx);
}
```

### `std::variant` for Type-Safe Unions

```cpp
using Result = std::variant<Success, Error>;

Result process(Input in) {
    if (valid(in)) return Success{compute(in)};
    return Error{"invalid input"};
}

std::visit(overloaded{
    [](const Success& s) { handle_success(s); },
    [](const Error& e)   { handle_error(e); },
}, process(input));
```

---

## Const Correctness

Apply `const` at every opportunity. Const is documentation, and the compiler enforces it.

```cpp
// Bad: nothing is const
std::string get_name() { return name_; }
void process(std::string s) { ... }

// Good: const communicates intent
std::string get_name() const { return name_; }    // member function doesn't modify *this
void process(const std::string& s) { ... }        // parameter not modified, passed by ref
const std::string& name() const { return name_; } // return const ref for expensive types
```

**Rule:** every member function that does not modify state should be `const`. Every parameter that is not modified should be `const`. Every local variable that is not reassigned should be `const`.

---

## Move Semantics

### When to Use `std::move`

Use `std::move` when transferring ownership of a resource from a named variable:

```cpp
// Moving into a container — avoids copy
std::vector<std::string> names;
std::string s = "hello";
names.push_back(std::move(s)); // s is now in a valid but unspecified state

// Moving from a function parameter you won't use again
void store(std::string value) {
    stored_ = std::move(value); // avoid copy into stored_
}
```

**Do not** use `std::move` on the return expression of a function — Named Return Value Optimization (NRVO) is better:

```cpp
// Bad: inhibits NRVO
std::string make_string() {
    std::string s = "hello";
    return std::move(s); // prevents copy elision
}

// Good: let the compiler optimize
std::string make_string() {
    std::string s = "hello";
    return s; // NRVO applies
}
```

---

## Naming Conventions

Follow a consistent style. The most important rule is **consistency within a codebase**.

| Entity | Convention | Example |
|--------|-----------|---------|
| Types (class, struct, enum) | `PascalCase` | `OrderBook`, `TradeResult` |
| Functions and methods | `snake_case` | `submit_order()`, `get_price()` |
| Variables | `snake_case` | `order_count`, `last_price` |
| Private members | `snake_case_` (trailing underscore) | `price_`, `order_id_` |
| Constants (`constexpr`, `const`) | `kPascalCase` or `SCREAMING_SNAKE` | `kMaxOrders`, `MAX_RETRIES` |
| Template parameters | `PascalCase` | `template <typename T>`, `template <typename Container>` |
| Macros | `SCREAMING_SNAKE_CASE` | `ASSERT_VALID(x)` |
| Namespaces | `snake_case` | `trading::execution::` |

---

## Undefined Behavior — Common Pitfalls

Undefined behavior is silent, reproducible only sometimes, and can manifest in completely unrelated code.

**Signed integer overflow:** use `int64_t` or check before arithmetic.
```cpp
// UB: signed overflow
int a = INT_MAX;
int b = a + 1; // undefined behavior

// Safe
int64_t a = INT_MAX;
int64_t b = a + 1; // defined
```

**Out-of-bounds access:** use `.at()` for checked access, or assert bounds.
```cpp
std::vector<int> v = {1, 2, 3};
v[5];      // UB: out of bounds
v.at(5);   // throws std::out_of_range
```

**Dangling references:** never return a reference to a local variable.
```cpp
const std::string& bad() {
    std::string s = "hello";
    return s; // UB: reference to destroyed local
}
```

**Null pointer dereference:** always check before dereferencing raw pointers from external sources.

---

## Performance-Sensitive Patterns

### Pass by Value vs Reference

| Type size | Not modified | Modified |
|-----------|-------------|---------|
| Small (≤ pointer size) | Pass by value | Pass by value, return new |
| Large (string, vector, etc.) | `const T&` | `T&` or pass by value + move |
| Ownership transfer | `T&&` (move) or `T` (sink) | — |

### Reserve Before Fill

```cpp
std::vector<double> prices;
prices.reserve(expected_size); // avoids reallocations
for (const auto& tick : ticks) {
    prices.push_back(tick.price);
}
```

### Prefer `emplace_back` over `push_back`

```cpp
std::vector<std::pair<int, std::string>> v;
v.push_back({1, "hello"});           // constructs temporary, then moves
v.emplace_back(1, "hello");          // constructs in place — one fewer operation
```

---

## C++17/20 Features to Prefer

| Feature | Prefer Over |
|---------|------------|
| Structured bindings `auto [k, v] = *it;` | `it->first`, `it->second` |
| `if` with init `if (auto p = map.find(k); p != map.end())` | Separate declaration |
| `std::string_view` | `const std::string&` for read-only string params |
| `[[nodiscard]]` | Ignoring return values silently |
| `std::span<T>` (C++20) | Pointer + length pairs |
| Concepts (C++20) | SFINAE for template constraints |
| `std::format` (C++20) | `printf`, `ostringstream` |
