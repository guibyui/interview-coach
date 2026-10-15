# Testing Strategy Questions

## Expected shape of a strong answer
Frame the answer around the **testing pyramid** explicitly and explain why the shape exists before mentioning any personal tooling or process:
- Many cheap, fast **unit tests** at the base: they pin down logic precisely and run in milliseconds.
- Fewer **integration tests** in the middle: they check that real pieces connect (code and database, service and queue).
- A handful of expensive **end-to-end tests** at the top: slow and brittle, so reserved for critical user journeys.
The reason: catch most bugs cheaply and quickly, save slow tests for what truly needs them. Then ground the answer in a concrete example from the candidate's own work.

## Questions

### 1. Walk me through your testing strategy. How do unit, integration, and end-to-end tests differ, and how do you decide the mix?
Key points: the pyramid and why; a concrete example at each layer (a pure pricing calculation as a unit test; saving an order and reading it back with the right customer and addresses as an integration test; a critical checkout flow as end-to-end); decide the mix by risk and cost of failure.

### 2. Give an example of something you would unit test versus something only an integration test would catch.
Key points: unit tests cover pure logic and edge cases (zero quantity, rounding, discounts); integration tests catch wiring problems that mocks hide (a wrong column name, a transaction that does not commit, a serialization mismatch, a migration that breaks a query).

### 3. Where is the line between mocking a dependency and testing against the real thing?
Key points: mock things that are slow, non-deterministic, costly, or outside your control (third-party APIs, clocks, random numbers, email providers); do not mock what you own and can run cheaply (your database in a test container); over-mocking makes tests pass while the system is broken; use fakes over deep mocks where possible; prefer a few contract or sandbox tests against the real service to verify your assumptions about its behavior.

### 4. How do you test a third-party API integration?
Key points: unit tests use mocked responses shaped from the provider's documentation or from one real recorded call (no network); a small number of integration tests hit the provider's sandbox; **contract tests** verify the shape you depend on has not changed; test failure paths (timeouts, rate limits, malformed responses); monitor in production and alert on error rates. Common confusion to correct: a unit test never makes the real network call.

### 5. What makes end-to-end tests expensive to maintain, and how do you decide which journeys deserve one?
Key points: slow, flaky because of timing and shared state, expensive to debug, brittle to UI changes; keep them for revenue-critical or security-critical flows (sign up, checkout, payment); use stable selectors, isolated test data, and retries only where the flake is understood; push everything else down the pyramid.

### 6. How do you test a system with complex business logic and heavy validation (pricing, tax, coupon math)?
Key points: isolate the rules in pure functions; table-driven or parameterized tests with boundary values; property-based testing for invariants (total never negative, discounts never exceed price); a handful of golden-path integration tests; regression tests for every bug found; compare against known-good examples from the business.

### 7. What should you NOT test?
Key points: do not test third-party libraries (trust their tests); do not test trivial getters and setters that carry no logic; do not duplicate coverage that integration or end-to-end tests already provide; decide by asking how bad it would be if this broke in production, and test in proportion to that risk. Chasing a coverage percentage for its own sake is a smell.

### 8. What is snapshot testing, and when is it more useful than a standard assertion?
Key points: the first run records output as the baseline; later runs compare against it and fail on any difference, which a human then approves or fixes. Best for large, structured, deterministic output (rendered components, serialized API responses, generated reports, a parsed structured object) where hand-written assertions for every field would be tedious. Pitfalls: noisy snapshots (timestamps, IDs, natural variation) cause flaky tests; people approve changes without reading them; huge snapshots stop being reviewed. For AI output, snapshot the validated, structured result (schema and extracted fields), not free-form model prose.

### 9. How do you test code that calls an LLM?
Key points: mock the model call in unit tests with canned responses, including malformed ones; test parsing and validation of the output against a schema; test retry and fallback behavior; keep a small evaluation set of real inputs with expected structured outputs for regression checks when prompts or models change; accept that exact-text assertions are fragile and assert on structure and key facts instead.

### 10. Tell me about a time a bug slipped through your tests. What layer was missing and what did you change?
Key points: a real story; honest identification of the missing layer; the specific change (a new test type, a contract test, better test data, monitoring); a result showing the same class of bug was caught afterward.

### 11. What is test-driven development, and when does it help or hurt?
Key points: red-green-refactor; helps with design, regression safety, and confidence in refactoring; hurts when requirements are unclear, for exploratory spikes, or when applied dogmatically to glue code. Strong answers show judgment instead of loyalty to a method.

## Follow-up probes
- How do you keep the suite fast as it grows?
- How do you handle flaky tests?
- What do you do about test data?
- How do you decide what runs in CI on every commit versus nightly?
- How do you test concurrency or timing-sensitive code?
