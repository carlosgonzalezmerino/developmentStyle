# Design principles and patterns

Prefer object-oriented design (OOP) when structuring application code. OOP is a programming paradigm, not a design pattern. Apply individual patterns when they solve a concrete problem more clearly than a simpler alternative. Follow the [development style](carlos_development_style.md).

## Object-oriented design (OOP)

- **Default direction:** Model cohesive responsibilities as objects that keep related state and behavior together; expose clear interfaces and encapsulate implementation details.
- **Prefer composition:** Assemble collaborators through explicit dependencies rather than building deep inheritance hierarchies.
- **Keep boundaries clear:** Place business rules in domain objects or services, and data access in DAOs. Each class and method should have one responsibility.
- **Avoid unnecessary ceremony:** Do not create classes, interfaces, or inheritance solely to satisfy OOP; a small stateless function or simple data structure is appropriate when clearer.

## Data Access Object (DAO)

- **Intent:** Encapsulate access to a data source behind operations that callers can use without knowing the persistence details.
- **Use when:** Several parts of the application need consistent access to a database or another data source, and isolating queries and data mapping makes the code easier to maintain or test.
- **Avoid when:** A small, direct data-access function is sufficient; do not add an interface and implementation solely to wrap one query.
- **Example:** An `OrderDao` provides `get_by_id(id)` and `save(order)`, while keeping SQL and row mapping out of the calling service.
- **Boundary:** Keep business rules in the service or domain layer; the DAO owns data access and mapping, not business decisions. Use parameterized queries and never embed credentials.

## Builder

- **Intent:** Construct a complex object step by step while keeping its creation readable and its final state valid.
- **Use when:** Construction has many optional settings or validation rules, and a long constructor obscures what each value means.
- **Avoid when:** A constructor or named arguments already make creation clear; do not add a builder for a simple object.
- **Example:** `ReportBuilder` sets a date range and selected sections, then `build()` validates them and returns a `Report`.
- **Boundary:** Keep business behavior in the resulting object or its service; the builder owns construction and validation of construction inputs.

## Strategy

- **Intent:** Encapsulate interchangeable behavior behind a shared contract so the caller can select an implementation without changing its own flow.
- **Use when:** Multiple algorithms or policies need to vary independently, such as distinct pricing rules.
- **Avoid when:** There is only one behavior or a small conditional is clearer than multiple classes.
- **Example:** An order service calls `calculate(order)` on a pricing strategy chosen for the current context.
- **Boundary:** Make strategy selection explicit and keep each strategy focused on one behavior; do not hide selection in a global registry.

## Factory

- **Intent:** Centralize object creation when choosing or configuring a concrete implementation would otherwise leak into callers.
- **Use when:** Several concrete implementations share a contract and creation depends on explicit configuration or context, or setup is complex enough to merit one construction point.
- **Avoid when:** A direct constructor or small creation function is clearer; do not introduce factories or class hierarchies just to create a single known type.
- **Example:** `create_exporter(format)` returns a CSV or JSON exporter based on a validated format.
- **Boundary:** The factory owns selection and construction, not the exporter's business behavior. Keep selection explicit; do not turn it into a hidden service locator.

## Patterns and practices we avoid

Prefer explicit, understandable dependencies and local state. The following are defaults to avoid, not absolute bans; document a concrete reason when an exception is necessary.

- **Dependency injection containers and autowiring:** Avoid frameworks that assemble object graphs implicitly when simple explicit construction is enough. Passing a collaborator via a constructor or method parameter is fine; dependency injection is a technique, not a GoF pattern. Prefer wiring dependencies explicitly at the application boundary.
- **Service Locator:** Avoid fetching collaborators from a global registry inside business logic; it hides dependencies and complicates tests. Pass required collaborators explicitly instead.
- **Singletons with mutable global state:** Avoid process-wide mutable objects that couple unrelated code and make tests order-dependent. Prefer scoped instances with explicit ownership; immutable shared values are not the concern here.
- **Deep inheritance hierarchies:** Avoid extending multiple levels of classes solely to reuse behavior; changes in a base class can affect distant subclasses. Prefer composition or a small interface unless inheritance expresses a stable, genuine subtype relationship.
