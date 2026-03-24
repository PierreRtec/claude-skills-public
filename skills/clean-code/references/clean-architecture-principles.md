# Clean Architecture Principles (Robert C. Martin)

Macro principles: from module structure to system architecture.

## 1. The fundamental rule

> *"The goal of software architecture is to minimize the human resources required to build and maintain the required system."*

- Design quality = effort required to maintain the system over time
- If effort grows with each release, the design is bad
- "The only way to go fast, is to go well"
- "Making messes is always slower than staying clean, no matter which time scale you are using"

## 2. The two values of software (Ch. 2)

| Value | Description | Urgency | Importance |
|---|---|---|---|
| **Behavior** | What the software does (features, bugs) | Often urgent | Not always important |
| **Structure** | How easy it is to change the software | Rarely urgent | Always important |

- **Structure > Behavior.** Software that's easy to change but buggy has more value than perfect software that's impossible to modify.
- A program that works but can't be changed will become useless when requirements evolve.
- A program that doesn't work but is easy to change can be fixed and will remain useful.
- Developers must fight for architecture. It's their responsibility, not management's.

## 3. SOLID (Part III)

### SRP: Single Responsibility Principle

> *"A module should be responsible to one, and only one, actor."*

- It's NOT "a function does one thing" (that's a function rule, not SRP)
- An **actor** = a group of people who want the same change
- If the CFO and the COO depend on the same module, a change for one risks breaking the other
- **Solution**: separate code that different actors depend on into different classes/modules
- At component level: Common Closure Principle
- At architecture level: Axis of Change for Architectural Boundaries

### OCP: Open-Closed Principle

> *"A software artifact should be open for extension but closed for modification."*

- Behavior must be extensible without modifying existing code
- Organize dependencies so high-level components are protected from changes in low-level components
- The most protected component = the one containing business rules (the Interactor)
- Protection hierarchy: Entities > Use Cases > Controllers > Presenters > Views
- Dependency arrows point toward what you want to protect

### LSP: Liskov Substitution Principle

> *"Subtypes must be substitutable for their base types."*

- Applies to interfaces, APIs, contracts, not just OOP inheritance
- Classic violation: the Square/Rectangle problem (a square is not a substitutable rectangle)
- At architecture level: if a REST service has a contract, all implementors must respect it. No `if type == "special" then ...`
- Violation = explosion of if/else/switch on types = coupling and fragility

### ISP: Interface Segregation Principle

> *"Don't depend on things you don't use."*

- Fine and specific interfaces rather than fat interfaces
- If a class uses 3 methods out of 30 in an interface, it depends on 27 useless methods
- Each change on those 27 methods forces recompilation/redeployment
- At architecture level: don't depend on an entire framework if you use 5% of its features

### DIP: Dependency Inversion Principle

> *"High-level code must not depend on low-level code. Both must depend on abstractions."*

- Source code dependencies point toward abstractions, not concretions
- Stable and important components (business rules) must NOT import volatile components (UI, DB, frameworks)
- **Factories**: use the Abstract Factory pattern to create concrete instances without high-level code knowing the concrete classes
- In practice: interfaces are defined in the high-level layer, implemented in the low-level layer

## 4. The Dependency Rule (Ch. 22)

```
+--------------------------------------------------+
|                  Frameworks & Drivers             |
|  +--------------------------------------------+  |
|  |            Interface Adapters               |  |
|  |  +--------------------------------------+  |  |
|  |  |             Use Cases                 |  |  |
|  |  |  +--------------------------------+  |  |  |
|  |  |  |           Entities              |  |  |  |
|  |  |  +--------------------------------+  |  |  |
|  |  +--------------------------------------+  |  |
|  +--------------------------------------------+  |
+--------------------------------------------------+

Dependency arrows point ONLY inward.
```

### The layers

| Layer | Contains | Examples |
|---|---|---|
| **Entities** | Universal business rules, domain objects | `User`, `Order`, `Invoice` |
| **Use Cases** | Application-specific business rules | `CreateOrder`, `ValidatePayment` |
| **Interface Adapters** | Data conversion between use cases and the outside world | Controllers, Presenters, Gateways |
| **Frameworks & Drivers** | Technical details | Express, React, PostgreSQL, S3 |

### Strict rules

1. **Nothing in an inner circle knows anything about an outer circle**: no import, no name mention, nothing.
2. **Data crossing boundaries** are simple structures (DTOs, plain objects), NOT entities or ORM objects.
3. **Flow of control** can go in the opposite direction of dependencies thanks to DIP (interfaces).
4. **The framework is a detail**: the app's core doesn't know which framework it uses.

## 5. Boundaries (Ch. 17-18)

### Principles

- **UI, DB, frameworks = details**, not the architecture. They are plugins.
- **Business rules are the core.** Everything else is peripheral.
- Draw lines between what's important (business rules) and what's a detail (I/O, DB, UI, framework)
- **Plugin Architecture**: the core doesn't know plugins exist. Plugins know the core.

### In practice

- The DB is a detail. The core defines a `UserRepository` interface, the external layer implements `PostgresUserRepository`.
- The web framework is a detail. The core defines a `CreateUser` use case, the HTTP controller calls it.
- The UI is a detail. The presenter formats data, the view displays it.

### Partial Boundaries (Ch. 24)

You don't always need full Clean Architecture. Pragmatic options:
- **Strategy Pattern**: interface + implementation, without separating into components
- **Facade**: a class that hides complexity behind a simple API
- **Skip the Last Step**: prepare interfaces without creating separate components (separate them when the need is real)

## 6. Screaming Architecture (Ch. 21)

> *"The architecture should scream the intent of the system."*

- The project structure must scream its business domain, not its framework
- A `health-checks/` or `invoices/` folder says more than `controllers/` or `services/`
- If someone looks at the structure, they should understand "it's a billing app" not "it's an Express app"
- **"Frameworks are tools, not ways of life"**: don't marry a framework, use it as a tool

### Package by Feature, not by Layer

```
# Bad (by technical layer)
src/
  controllers/
  services/
  repositories/
  models/

# Good (by feature/domain)
src/
  users/
    create-user.ts
    user-repository.ts
    user.ts
  invoices/
    create-invoice.ts
    invoice-repository.ts
    invoice.ts
```

## 7. Component Principles (Ch. 13-14)

### Cohesion

| Principle | Rule | In practice |
|---|---|---|
| **CCP** (Common Closure) | What changes together lives together | If a business rule change touches 3 files, they must be in the same component |
| **CRP** (Common Reuse) | What's used together lives together | Don't force a user to depend on things they don't use |
| **REP** (Reuse/Release Equivalence) | Release unit = reuse unit | A reusable component must be independently releasable |

### Coupling

| Principle | Rule | In practice |
|---|---|---|
| **ADP** (Acyclic Dependencies) | No cycles in the dependency graph | If A depends on B and B depends on A, extract the common interface |
| **SDP** (Stable Dependencies) | Depend in the direction of stability | Volatile components depend on stable components, not the reverse |
| **SAP** (Stable Abstractions) | Stable components must be abstract | A stable and concrete component is painful to change. Abstract it. |
