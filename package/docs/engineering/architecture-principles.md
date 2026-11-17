# Architecture Principles

This document defines the team's default architectural guidance. The approved project architecture and its Architecture Decision Records (ADRs) are authoritative when they make a more specific choice.

## General approach

- Start from business requirements, quality attributes, and the shape of the domain. Do not select an architecture by pattern name alone.
- Prefer the simplest design that keeps important responsibilities and change boundaries clear. Add abstractions and layers when they solve an identified problem.
- Keep dependencies explicit and point them toward stable policy and domain rules. Record significant, lasting tradeoffs in an ADR.
- Preserve existing conventions unless the task includes an approved reason to change them.

## Domain-Driven Design

Use DDD techniques when the software has meaningful business rules, domain language, or workflows that benefit from an explicit domain model. For simple data-oriented features, use a simpler model.

When DDD is appropriate:

- Use the language of domain experts consistently in names and discussions.
- Model entities by identity and lifecycle; use value objects for concepts defined by their values and invariants.
- Define aggregate boundaries around invariants that must be kept consistent. Keep aggregates small and update them through their roots.
- Place domain invariants with the domain concepts that own them. Use domain services only for domain behavior that does not naturally belong to one entity or value object.
- Use repositories to express aggregate persistence needs when useful; avoid exposing persistence details in the domain model.
- Define bounded contexts and their contracts when separate parts of the system use different models or have independent ownership.
- Do not introduce CQRS, event sourcing, domain events, or a repository abstraction by default. Adopt them only to address a stated requirement or a documented tradeoff.

## Clean Architecture and dependency boundaries

Use dependency inversion to keep core policy independent of delivery mechanisms and infrastructure. Adapt the layer names to the repository; a common arrangement is Domain, Application, Infrastructure, and Presentation/API.

- Domain contains business concepts and invariants and does not depend on application, infrastructure, or presentation code.
- Application coordinates use cases and depends on domain abstractions. It should not depend on concrete infrastructure implementations.
- Infrastructure implements external concerns such as persistence, messaging, and third-party services, and depends inward on abstractions it implements.
- Presentation/API translates external requests and responses and delegates work to application use cases. Keep business rules out of controllers and endpoints.
- Composition roots wire implementations to abstractions. Avoid service location and hidden dependencies.
- Keep contracts at the narrowest useful boundary. Avoid projects and interfaces that have no responsibility or dependency boundary to enforce.
- For .NET applications that use Clean Architecture, represent Domain, Application, Infrastructure, and API/Presentation as separate projects, with project references pointing inward. Layer folders inside one project do not provide the same compile-time dependency boundaries.
- Combining layers or using a single project is an exception. Present its tradeoffs during Architecture and Design and proceed only after explicit user approval.

## Composition over inheritance

- Prefer composition and interfaces over deep inheritance hierarchies.
- Use inheritance only for genuine "is-a" relationships where derived types remain behaviorally substitutable for their base type.
- Consider strategy or decorator patterns when they express variable behavior or layered responsibilities more clearly than inheritance.

## SOLID principles

- **Single Responsibility:** Give each class or module one clear reason to change. Separate responsibilities when they change for unrelated reasons.
- **Open/Closed:** Keep behavior open to extension and closed to modification where stable extension points solve a real need. Do not add speculative abstractions for hypothetical changes.
- **Liskov Substitution:** Derived types must be usable in place of their base types without violating expected behavior, contracts, errors, or lifecycle rules.
- **Interface Segregation:** Prefer focused interfaces tailored to their consumers over general interfaces that make callers depend on unused operations.
- **Dependency Inversion:** Make high-level policy depend on abstractions at boundaries, not on concrete infrastructure details. Keep dependencies explicit and the number of abstractions proportional to actual variation or testing needs.

## Architecture review

For a material change, describe affected components, dependency direction, data and API contracts, and operational concerns in the architecture document or feature design. Compare the design with existing ADRs. Record a new ADR when the decision has lasting consequences, meaningful alternatives, or is difficult to reverse.
