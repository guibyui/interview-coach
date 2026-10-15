# SOLID Principles and OOP Design Questions

## Contents
- Expected shape of a strong answer
- The five principles (definition, questions, key points, classic violation, pitfalls)
- Cross-cutting questions
- Code-review exercises
- Follow-up probes

## Expected shape of a strong answer
For each principle a strong candidate does four things:
1. **Defines it in plain words**, not just by reciting the name.
2. **Shows a violation** with a concrete example, ideally from their own code.
3. **Shows the fix** and says what it buys (testability, safe change, fewer regressions).
4. **Says when not to apply it.** SOLID is a set of guidelines for managing change, not a goal. Candidates who treat it as a checklist, or who build an interface for everything, show weaker judgment than ones who name the cost of over-applying it.

Level calibration: juniors should define the five and give a simple example of each; mid-level candidates should spot violations in code and refactor them; seniors should discuss trade-offs, over-engineering, how the principles apply outside classic OOP (functions, modules, TypeScript, Python), and how they apply them in team decisions.

---

## S: Single Responsibility Principle (SRP)

**Definition.** A module should have one reason to change. Robert Martin's clarification: it should be responsible to one actor, meaning one group of stakeholders who would request changes.

**Questions**
- Explain the Single Responsibility Principle. Does it mean a class should do only one thing?
- How do you recognize a class with too many responsibilities?

**Key points**
- Common misreading: "a class does one thing." The real test is *reasons to change*: if accounting, HR, and the DBA team could each ask for a change to the same class, it has too many responsibilities.
- Smells: a class name with "And" or "Manager"/"Utils"/"Helper" that grows forever; tests that need many unrelated mocks; merge conflicts from unrelated work in one file; a change in one feature breaking another.
- Violation example: an `OrderProcessor` that validates input, calculates tax, charges the card, writes to the database, sends the email, and formats the receipt.
- Fix: separate by reason to change (validation, pricing, payment, persistence, notification), with a thin coordinating service.
- Pitfall: splitting into dozens of tiny classes with no cohesion. The goal is cohesion, not class count.

## O: Open/Closed Principle (OCP)

**Definition.** Software entities should be open for extension but closed for modification: add new behavior without editing tested, working code.

**Questions**
- Explain the Open/Closed Principle with an example.
- How would you refactor a long `if/else` or `switch` on a type string?

**Key points**
- Violation: a function with `switch (carrier) { case "ups": ...; case "fedex": ... }` that must be edited every time a carrier is added, risking regressions in the other branches.
- Fix: polymorphism or the strategy pattern: a `ShippingRateCalculator` interface with one implementation per carrier, chosen by a registry or factory. A new carrier is a new class, and existing code is untouched. In functional style: pass behavior as a function, or look up handlers in a map.
- Where it applies: places where variation is likely and has actually appeared (payment providers, discount rules, export formats, notification channels).
- Pitfall: speculative abstraction. Apply the "rule of three": tolerate the first variation, generalize at the second or third. An extension point for a change that never comes is just indirection.
- Real change still requires modification sometimes; "closed" is about the core staying stable, not never touching code.

## L: Liskov Substitution Principle (LSP)

**Definition.** Objects of a subtype must be usable anywhere the base type is expected without breaking correctness. (Barbara Liskov.)

**Questions**
- Explain the Liskov Substitution Principle. Give an example of a violation.
- Why is the Square/Rectangle example a violation?
- How do you detect LSP violations in a codebase?

**Key points**
- Behavioral contract, not just matching method signatures. A subtype must not strengthen preconditions, must not weaken postconditions, must preserve the base type's invariants, and must not throw exceptions the base contract does not allow.
- Classic violations: `Square extends Rectangle` where setting width also changes height, breaking code that assumes the two are independent; `Penguin extends Bird` where `fly()` throws; a `ReadOnlyList` that extends `List` and throws on `add()`.
- Smells: `instanceof` or type checks on subclasses in client code; overrides that throw "not implemented" or silently do nothing; subclasses that need to know about their siblings.
- Fix: rethink the hierarchy: separate interfaces (`Flyer` versus `Bird`), composition over inheritance, or make immutability part of the type so the contract is honest.
- "Is-a" in the real world is not the same as behavioral substitutability in code.

## I: Interface Segregation Principle (ISP)

**Definition.** Clients should not be forced to depend on methods they do not use. Prefer several small, role-specific interfaces to one large general-purpose one.

**Questions**
- Explain the Interface Segregation Principle with an example.
- How does ISP relate to SRP?

**Key points**
- Violation: a `Machine` interface with `print()`, `scan()`, and `fax()`, forcing a simple printer to implement `scan()` and `fax()` with empty or throwing methods (which then also breaks LSP). Or a fat `UserRepository` that a read-only report is forced to depend on, including write methods.
- Fix: role interfaces (`Printer`, `Scanner`) that classes implement as needed; consumers depend only on the slice they use.
- Benefits: lower coupling, simpler fakes in tests, changes in unused methods no longer force recompiles or redeploys of clients.
- In TypeScript, small structural types or `Pick<>` achieve this; in Go, small interfaces defined by the consumer are idiomatic.
- Relationship to SRP: SRP is about why a *module* changes; ISP is about what a *client* is forced to know. Fat interfaces often signal classes with multiple responsibilities.
- Pitfall: an explosion of one-method interfaces that no one can navigate.

## D: Dependency Inversion Principle (DIP)

**Definition.** High-level modules should not depend on low-level modules; both should depend on abstractions. Abstractions should not depend on details; details should depend on abstractions.

**Questions**
- Explain the Dependency Inversion Principle.
- What is the difference between Dependency Inversion, Dependency Injection, and Inversion of Control?
- How does DIP make code easier to test?

**Key points**
- Violation: `OrderService` constructs an `SmtpMailer` or imports a vendor SDK directly, so business logic is tied to one provider and cannot be tested without sending mail or mocking the SDK internals.
- Fix: `OrderService` depends on a `Notifier` interface that *the high-level module defines* (the "inversion": the low-level detail conforms to the needs of the business layer). A concrete adapter wraps the vendor and is passed in from outside.
- **Distinctions:** DIP is the *principle* (depend on abstractions, ownership of the interface sits with the high-level code). **Dependency Injection** is a *technique* for supplying dependencies from outside (constructor, parameter). **Inversion of Control** is the broader idea that a framework or container calls your code instead of you calling it. A **DI container** is optional tooling.
- Testability: pass a fake `Notifier` in tests; no network calls, fast and deterministic.
- Pitfall: an interface for everything, even classes with exactly one implementation that will never change, and heavy DI-container "magic" that hides the wiring. Keep abstractions at real boundaries (I/O, third parties, things that change independently).

---

## Cross-cutting questions

1. Which SOLID principle do you find most valuable day to day, and which is most often violated? Why?
2. Can SOLID be over-applied? Describe what over-engineering looks like. (Look for: abstractions with a single implementation, deep inheritance trees, "factory of factories", premature generalization; mention YAGNI and KISS as counterweights.)
3. How do SOLID principles apply outside classic OOP, such as in functional code, JavaScript or TypeScript modules, or Python? (Functions as strategies, modules as units of responsibility, dependency passing instead of containers, duck typing and protocols for ISP and DIP.)
4. How do the SOLID principles relate to design patterns? (Strategy and Decorator serve OCP; Adapter and Facade serve DIP and ISP; Factory and Dependency Injection help DIP; patterns are solutions, principles are the reasons.)
5. Composition over inheritance: what does it mean and when does inheritance still make sense? (Inheritance couples subtype to parent implementation and invites LSP problems; composition is more flexible. Inheritance is fine for stable, true behavioral is-a relationships and framework extension points.)
6. How does SOLID support testing and maintainability? (Small, focused units; seams where fakes plug in; changes localized.)
7. How do you introduce these principles to a team without turning code review into dogma? (Explain the cost of change, use examples from the team's own code, prefer small refactors when touching code, agree on where abstractions are worth it.)
8. Tell me about a time you refactored code to improve its design. What principle guided you, and how did you know it was worth it?
9. Tell me about a time you decided *not* to apply a principle. Why? (A strong answer shows judgment: the code was stable, deadline pressure, premature abstraction risk.)
10. Beyond SOLID, what other design principles do you use? (DRY, KISS, YAGNI, Law of Demeter, separation of concerns, high cohesion and low coupling, composition over inheritance, fail fast.) Note that DRY is about duplicated *knowledge*, not duplicated characters; merging code that only looks similar can couple unrelated things.

## Code-review exercises

Describe the code aloud or paste it, then ask: which principles are violated, what would you change, and what would you leave alone? Expected answers are below.

**Exercise 1: the do-everything service**
```ts
class OrderService {
  place(order: Order) {
    if (!order.items.length) throw new Error("empty");
    const tax = order.country === "US" ? order.total * 0.07
              : order.country === "BR" ? order.total * 0.12
              : 0;
    stripe.charges.create({ amount: order.total + tax });
    db.query("INSERT INTO orders ...");
    new SmtpMailer().send(order.email, "Thanks!");
  }
}
```
Expected: SRP (validation, tax, payment, persistence, and email are all in one place), OCP (adding a country edits the tax code), DIP (direct dependence on the Stripe SDK, a database client, and a concrete mailer, making unit testing hard). Refactor: separate collaborators behind small interfaces (`TaxPolicy`, `PaymentGateway`, `OrderRepository`, `Notifier`), injected into `OrderService`, which only orchestrates. Note what to leave alone: a trivial validation check may stay in the service.

**Exercise 2: the type switch**
```ts
function shippingCost(parcel: Parcel, carrier: string) {
  switch (carrier) {
    case "ups": return parcel.weight * 1.2;
    case "fedex": return parcel.weight * 1.1 + 3;
    case "usps": return parcel.weight * 0.9 + 5;
  }
}
```
Expected: OCP. Refactor with a strategy per carrier, or a map from carrier to rate function. Ask: when would you *not* refactor? (Three stable carriers that rarely change; the switch may be the clearest option.)

**Exercise 3: the flightless bird**
```ts
class Bird { fly() { /* ... */ } }
class Penguin extends Bird { fly() { throw new Error("cannot fly"); } }
```
Expected: LSP. Code that calls `bird.fly()` breaks for penguins. Fix: separate `Bird` from `FlyingBird`, or use composition (a `FlightBehavior`), or an interface `Flyer`.

**Exercise 4: the fat interface**
```ts
interface Machine { print(doc: Doc): void; scan(): Doc; fax(doc: Doc): void; }
class BasicPrinter implements Machine {
  print(doc: Doc) { /* ... */ }
  scan(): Doc { throw new Error("unsupported"); }
  fax(doc: Doc) { throw new Error("unsupported"); }
}
```
Expected: ISP (and LSP, because the unsupported methods throw). Fix: split into `Printer`, `Scanner`, `Fax`, and let multi-function devices implement several.

**Exercise 5: the hidden dependency**
```ts
class ReportGenerator {
  generate() {
    const rows = new SqlServerClient("prod-connection").query("SELECT ...");
    return new PdfRenderer().render(rows);
  }
}
```
Expected: DIP (hard-wired database and renderer; cannot test without production access), SRP (querying and rendering are separate concerns). Fix: inject a `ReportDataSource` and a `ReportRenderer`.

## Follow-up probes
- What is the cost of this refactor, and how would you justify it to your manager?
- How would you refactor this safely if the code has no tests? (Write characterization tests first, refactor in small steps.)
- What is the smallest change that gets most of the benefit?
- Where would you stop abstracting?
- Which of these would you do now, and which would you wait on until a second case appears?
