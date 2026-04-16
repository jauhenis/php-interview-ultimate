---
title: 'Software Design & Architecture Concepts'
slug: '/answers/architecture_advanced'
---

# Software Design & Architecture Concepts

## Object-Oriented Programming (OOP) Fundamentals

### Invariance, Covariance, and Contravariance

These terms describe how subtyping between complex types (like return types or parameter types) relates to subtyping between their component types.

- **Invariance**: The type must match exactly.
- **Covariance**: Allows a child class to return a _more specific_ type than the parent (e.g., `parent::make(): Animal` can be overridden by `child::make(): Dog`). Supported in PHP 7.4+ for return types.
- **Contravariance**: Allows a child class to accept a _less specific_ (wider) type than the parent (e.g., `parent::eat(Dog)` can be overridden by `child::eat(Animal)`). Supported in PHP 7.4+ for parameter types.

### Composition vs Inheritance

- **Inheritance** ("is-a" relationship): Use when a class is a specialized version of another. Risk: Fragile Base Class problem, tight coupling.
- **Composition** ("has-a" relationship): Use when a class uses another class to perform a task. Benefit: More flexible, easier to change at runtime, lower coupling.
- **Rule of Thumb**: "Favor composition over inheritance."

### Why Getters and Setters are considered "Bad" (sometimes)

Overusing them can lead to **Anemic Domain Models**, where objects are just bags of data (DSPs - Data Structure Projections) with no behavior. It violates **Encapsulation** because you expose the internal state of the object.

- **Better Approach**: "Tell, Don't Ask." Instead of getting a value, performing logic, and setting it back, tell the object to perform the action itself.

---

## Service Locator vs Inversion of Control (IoC) Container

- **Service Locator**: A pattern where an object "reaches out" to a central registry to find its dependencies.
  - _Cons_: Hidden dependencies, harder to test, violates Dependency Inversion.
- **IoC Container (Dependency Injection)**: Dependencies are "pushed" into the object (via constructor or setter). The object is passive and doesn't know how its dependencies are created.
  - _Pros_: Explicit dependencies, easier to mock/test, decoupled code.

---

## GRASP (General Responsibility Assignment Software Patterns)

A set of 9 patterns/principles for assigning responsibilities to classes.

- **Information Expert**: Assign responsibility to the class that has the information necessary to fulfill it.
- **Creator**: Who should be responsible for creating a new instance of some class?
- **Controller**: Who should handle a system event?
- **Low Coupling**: Keep classes as independent as possible.
- **High Cohesion**: Keep related logic together in one class.
- **Indirection**: Use an intermediate object to decouple two components.
- **Polymorphism**: Handle alternatives based on type.
- **Pure Fabrication**: Create a class that doesn't represent a domain concept to achieve low coupling/high cohesion (e.g., a Service).
- **Protected Variations**: Protect elements from variations of other elements by wrapping the focus of instability with an interface.

---

## Architecture Principles

### Law of Demeter (Principle of Least Knowledge)

"Talk only to your immediate friends." An object should not "reach through" another object to get to a third object (e.g., avoid `$user->getAccount()->getTransactions()->getFirst()`).

### Anemic Domain Model

A domain model where objects have state (properties) but little or no logic (behavior). Logic is instead found in "Service" classes. This is often considered an anti-pattern in DDD.

### DDD (Domain-Driven Design)

An approach to software development that prioritizes the core domain and domain logic.

#### Key Building Blocks:

- **Entity**: An object with a unique identity (ID) that persists even if its attributes change (e.g., a `User`).
- **Value Object**: An object defined by its attributes, with no identity (e.g., an `Address`, `Money`). They should be immutable. Two value objects with the same attributes are considered identical.
- **Aggregate**: A cluster of domain objects that can be treated as a single unit. An Aggregate has an **Aggregate Root** (an Entity) through which all access to the aggregate happens.
- **Repository**: An interface for retrieving and storing Aggregates.
- **Domain Service**: A service that contains business logic that doesn't naturally fit into an Entity or Value Object.
- **DTO (Data Transfer Object)**: A simple object used to pass data between layers (no logic).

---

## Hexagonal (Ports & Adapters) vs Onion Architecture

Both aim to decouple the application core (business logic) from external concerns (DB, API, UI).

### Hexagonal (Ports & Adapters)

Proposed by Alistair Cockburn, it focuses on separating the "inside" (application core) from the "outside" (infrastructure).

- **Application Core**: Contains domain logic and is independent of external tools.
- **Ports**: Interfaces defined by the core for what it needs (driven ports, e.g., `UserRepositoryInterface`) or what it provides (driving ports, e.g., `UseCaseInterface`).
- **Adapters**: Concrete implementations that bridge the gap between the core and the outside world (e.g., `DoctrineUserRepository` as a driven adapter, or `UserController` as a driving adapter).
- **Key Idea**: The application can be used by different "drivers" (UI, CLI, Tests) and can use different "driven" tools (MySQL, MongoDB, In-Memory) without changing the core.

### Onion Architecture

Proposed by Jeffrey Palermo, it emphasizes concentric layers where dependencies always point inward.

- **Domain Model**: The innermost core (Entities, Value Objects).
- **Domain Services**: Logic that operates on the domain model.
- **Application Services**: Use cases that coordinate the domain objects to achieve a goal.
- **Infrastructure / UI**: The outermost layer containing databases, external APIs, and user interfaces.
- **Key Idea**: "Dependencies point inwards." This ensures that the core domain is always independent of the implementation details of the outer layers.

### Comparison

| Feature                | Hexagonal Architecture                                                   | Onion Architecture                                                       |
| ---------------------- | ------------------------------------------------------------------------ | ------------------------------------------------------------------------ |
| **Primary Focus**      | Inside vs Outside (Ports/Adapters)                                       | Concentric Layers (Inward Dependencies)                                  |
| **Internal Structure** | Doesn't strictly prescribe layers inside the hexagon                     | Prescribes specific layers (Domain, Application, etc.)                   |
| **Analogy**            | A hexagon with multiple "sides" (ports)                                  | An onion with multiple "skins" (layers)                                  |
| **Similarities**       | Both use Dependency Inversion to keep the core clean from infrastructure | Both use Dependency Inversion to keep the core clean from infrastructure |
