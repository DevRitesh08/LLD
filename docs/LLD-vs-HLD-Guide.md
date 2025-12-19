# LLD vs HLD — Guide

> HLD defines system structure; LLD defines code structure.

---

## 1) One‑Minute Summary

- HLD: Bird’s‑eye system architecture — what components exist and how they interact.
- LLD: Code‑level design — classes, interfaces, data structures, interactions.
- Sequence: HLD first (direction), then LLD (detailed build plan).
- Outputs: HLD → architecture views; LLD → class diagrams, method signatures, pseudo‑code.

---

## 2) What Is HLD?

- Purpose: Describe the overall architecture and major components of the system.
- Scope: Services/modules, data flow, integration points, protocols, storage choices, scaling strategies.
- Audience: Architects, tech leads, senior engineers, stakeholders.
- Typical artifacts:
  - Context and container/component diagrams
  - High‑level sequence/flow diagrams
  - API surface (endpoints, contracts at a high level)
  - Tech choices (DB, cache, broker, frameworks) and trade‑offs

### Definition

> High‑Level Design (HLD) describes the overall system architecture — its major components, data flow, and external interfaces — without going into code‑level implementation details.

---

## 3) What Is LLD?

- Purpose: Translate HLD into a code‑ready plan.
- Scope: Classes, interfaces, methods, data structures, relationships, algorithms, error handling, validations.
- Audience: Developers who will implement the system.
- Typical artifacts:
  - UML class/object/sequence diagrams at code granularity
  - Class responsibilities, attributes, and method signatures
  - Design patterns (Strategy, Factory, Observer, etc.)
  - Pseudo‑code, state transitions, edge cases, invariants

### Definition

> Low‑Level Design (LLD) specifies the detailed structure and behavior at the code level — classes, interfaces, methods, data structures, and interactions needed to implement the system.

---

## 4) Core Differences (HLD vs LLD)

- Level: HLD is abstract; LLD is concrete and code‑adjacent.
- Focus: HLD on architecture and components; LLD on classes and code structure.
- Questions: HLD answers “what are we building?”; LLD answers “how exactly will we build it?”
- Output: HLD → architecture diagrams and data flows; LLD → class diagrams, method contracts, pseudo‑code.
- Detail: HLD avoids coding details; LLD includes them.
- Patterns: HLD uses architectural patterns (microservices, CQRS, event‑driven); LLD uses code‑level patterns (Strategy, Factory, Adapter).
- UML: HLD uses context/container/component/sequence; LLD uses class/object/sequence/state.

---

## 5) Example: Food Delivery App

### HLD decides (what & how components interact)

- Services: User, Restaurant, Catalog, Order, Payment, Delivery, Notification.
- Data flow: Client → API Gateway → Services → DB/Cache → External Integrations (Payments, SMS, Push).
- Cross‑cutting: AuthN/Z, rate limiting, monitoring, logging, fault tolerance, scaling.

### LLD decides (code‑level structure)

- `User`, `Restaurant`, `MenuItem`, `Cart`, `Order`, `DeliveryAssignment` classes.
- `Order` lifecycle: Created → Confirmed → Preparing → Out‑for‑Delivery → Delivered/Cancelled (state transitions).
- Interfaces:
  - `PaymentStrategy` with implementations `CardPayment`, `UPIPayment`, `WalletPayment`.
  - `NotificationChannel` with `EmailChannel`, `SmsChannel`, `PushChannel`.
- Method signatures:
  - `Order.addItem(itemId, qty)`, `Order.calculateTotal()`, `Order.transitionTo(state)`.
  - `PaymentStrategy.pay(amount, txnContext)` returns `PaymentResult`.
- Patterns: Strategy for payments, Observer/Publisher for notifications, Factory for channel selection, State for order lifecycle.

---

## 6) Advantages of LLD

- Reduces coding ambiguity and rework.
- Improves code quality and consistency.
- Surfaces edge cases and invariants early.
- Encourages SOLID and clean abstractions.
- Eases onboarding and code reviews.

### Trade‑offs

- Time‑intensive for small/simple features.
- Risk of over‑design if scope is small.
- Requires strong OOP and pattern literacy.
- Changes later may be costlier if LLD is rigid.

---

## 7) LLD Essentials (What interviewers look for)

- Modeling: Identify entities, value objects, aggregates, relationships, and invariants.
- Behavior: Define method responsibilities, pre/post‑conditions, error handling.
- Boundaries: Clear interfaces; dependency direction (inversion where useful).
- Patterns: Strategy, Factory, Adapter, Observer, Decorator, State, Repository.
- Principles: SOLID, High cohesion, Low coupling, YAGNI on non‑essentials.
- Non‑functionals at code level: Idempotency, concurrency control, retries/backoff, timeouts.

---

## 8) Interview Flow (Step‑by‑Step)

1. Clarify scope and constraints (functional and non‑functional).
2. List core entities and their responsibilities.
3. Define relationships and state transitions (draw a simple state diagram if helpful).
4. Choose key patterns (and justify): where Strategy/Factory/Observer/State fit.
5. Draft class diagram with key attributes/methods and interfaces.
6. Walk through a core flow (e.g., “place order”) with sequence and method calls.
7. Address edge cases, failures, concurrency, and validations.
8. Summarize trade‑offs and extension points.

Use consistent, readable naming; keep it implementable within a sprint.

---

## 9) Quick Q&A

- What comes first: HLD or LLD?
  - HLD first for direction; LLD next for implementation details.
- Can LLD exist without HLD?
  - For small features, yes — but for systems, LLD should follow from an HLD baseline.
- What diagrams for LLD?
  - Class, object, sequence, and state diagrams at code granularity.
- How is LLD different from “system design”?
  - System design often refers to HLD; LLD is code‑level design of that system.
- How do SOLID principles apply?
  - Guide class responsibilities, dependency boundaries, and extensibility (e.g., Strategy for OCP).

---

## 10) One‑Liners (Cheat Sheet)

- HLD: Defines system structure and component interactions.
- LLD: Defines code structure and class‑level behavior.
- HLD → what & where; LLD → how & with what interfaces.
- HLD outputs architecture views; LLD outputs class diagrams and method contracts.

---

## 11) Practice Prompts (LLD)

- Design the LLD for a Rate Limiter.
- Design the LLD for a Parking Lot.
- Design the LLD for a News Feed.
- Design the LLD for a Hotel Booking Engine.
- Design the LLD for a Tic‑Tac‑Toe game with undo.

For each, follow the interview flow and explicitly call out entities, interfaces, state transitions, and chosen patterns.

---

## 12) Bonus: Tiny Pseudo‑Code Snippet (Payments Strategy)

```java
public interface PaymentStrategy {
  PaymentResult pay(Money amount, PaymentContext ctx);
}

public final class CardPayment implements PaymentStrategy {
  public PaymentResult pay(Money amount, PaymentContext ctx) {
    // tokenize card, call gateway, map response → PaymentResult
  }
}

public final class PaymentService {
  private final Map<PaymentMethod, PaymentStrategy> strategies;

  public PaymentResult checkout(Order order, PaymentMethod method) {
    var strategy = strategies.get(method);
    return strategy.pay(order.total(), PaymentContext.from(order));
  }
}
```

---

## 13) Tips to Stand Out

- Name key invariants (e.g., “Order total = sum(items) + fees”).
- Show evolution: “We start with Strategy; later we can add a Saga for distributed payments.”
- Call out failure modes and retries (idempotency keys, timeouts, backoff).
- Keep LLD implementable and testable; avoid gold‑plating.

---