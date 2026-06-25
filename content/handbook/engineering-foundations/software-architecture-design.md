---
title: "Software Architecture & Design"
weight: 10
description: "Architecture styles, the 12-Factor App, SOLID, and the GoF design patterns — the structural vocabulary that keeps software aligned with your goals and ready for change."
lastUpdated: 2026-06-25
goDeeper:
  - group: Books
    title: "Designing Data-Intensive Applications — Martin Kleppmann"
    url: "https://dataintensive.net/"
    why: "The clearest tour of the trade-offs behind distributed and data-centric architectures."
  - group: Books
    title: "Clean Architecture — Robert C. Martin"
    url: "https://www.oreilly.com/library/view/clean-architecture-a/9780134494272/"
    why: "Where SOLID and dependency direction come together into whole-system design."
  - group: Tools
    title: "Refactoring.Guru — Design Patterns Catalog"
    url: "https://refactoring.guru/design-patterns/catalog"
    why: "An illustrated, example-driven catalog of every classic GoF pattern."
  - group: Tools
    title: "The Twelve-Factor App"
    url: "https://12factor.net/"
    why: "The original, still the canonical reference for portable, cloud-ready apps."
---

Architecture is the set of decisions that are expensive to change later. Get them roughly right and your system stays aligned with your goals, absorbs new requirements gracefully, and survives the inevitable churn of technology and team. Get them wrong and every feature fights the structure. You don't need to be the one drawing the diagrams as a manager, but you do need the vocabulary to follow the conversation and ask good questions. Here are the foundations I lean on.

## Architecture styles

Architecture styles are the foundational blueprints for building software systems. Each one makes different trade-offs, and the right choice depends on the needs, constraints, and context of what you're building — not on what's fashionable. The most common styles:

- **Monolithic** — The entire application is built and served as a single unit; all functionality lives in one place.
- **Service-Oriented (SOA)** — The system is divided into individual services, each owning specific functionality and communicating with the others, which promotes reuse and lets each service be managed independently.
- **Component-Based** — The software is assembled from modular components, each providing a specific capability, that can be replaced, updated, or modified without disturbing the rest of the system.
- **Distributed Systems** — Components are spread across multiple machines or networks but present a unified service, improving scalability and reliability.
- **Event-Driven** — Components react to events or messages, acting when they receive specific notifications. This makes the system reactive and well-suited to asynchronous work.
- **Interpreter** — High-level code is translated to machine instructions line by line and executed directly rather than compiled ahead of time, trading performance for flexibility.
- **Data-Centric** — The system is organized around managing and using data, optimizing integrity, storage, and retrieval, with functionality built atop efficient data processing.

There's no "best" style — only the one that fits. Most real systems blend several.

## The 12-Factor App

The [12-Factor App](https://12factor.net/) is a concise methodology for building modern, portable, cloud-native applications. It's old enough now to be assumed knowledge, and it still holds up.

| # | Factor | What it means | TL;DR |
|----|----------------------|------------------------------------------------------------|---------------------------|
| 1 | **Codebase** | One codebase tracked in version control, many deploys | One repo per app |
| 2 | **Dependencies** | Declare and isolate dependencies explicitly | No global installs |
| 3 | **Config** | Store config in environment variables | No secrets in code |
| 4 | **Backing Services** | Treat services (DBs, queues, etc.) as attached resources | Swap without code change |
| 5 | **Build, Release, Run** | Separate the build, release, and run stages | Immutable releases |
| 6 | **Processes** | Execute the app as one or more stateless processes | No sticky sessions |
| 7 | **Port Binding** | Self-host via a port; don't rely on an external server | App runs standalone |
| 8 | **Concurrency** | Scale out via the process model | Run more instances |
| 9 | **Disposability** | Fast startup and graceful shutdown | Resilient to crashes |
| 10 | **Dev/Prod Parity** | Keep environments as similar as possible | Avoid "works on my machine" |
| 11 | **Logs** | Treat logs as event streams | Write to stdout/stderr |
| 12 | **Admin Processes** | Run admin tasks as one-off processes | One-off commands for migrations, fixes |

Together these are a checklist for portability, scalability, and maintainability across environments.

## SOLID principles

SOLID is a set of five design principles for writing maintainable, scalable, and robust object-oriented code. They're not rules to recite — they're a shared diagnosis language for *why* a class is painful to change.

| | Principle | What it says | TL;DR |
|--------|-----------|-------------|-------|
| **S** | **Single Responsibility** | A class should have one reason to change — it should do one thing. | One job per class |
| **O** | **Open/Closed** | Entities should be open for extension but closed for modification. | Add features without editing existing code |
| **L** | **Liskov Substitution** | Subclasses should be substitutable for their base classes without breaking behavior. | Child classes behave like their parents |
| **I** | **Interface Segregation** | Clients shouldn't be forced to depend on interfaces they don't use. | Small, focused interfaces |
| **D** | **Dependency Inversion** | Depend on abstractions, not concretions; high-level modules shouldn't depend on low-level ones. | Use interfaces and dependency injection |

Followed together, SOLID leads to cleaner, more testable, more flexible codebases.

{{< protip >}}
I treat SOLID as a vocabulary for code review, not a gate. When a reviewer says "this violates the Single Responsibility Principle," what they usually mean is "this class will be a nightmare to change six months from now." Naming the principle turns a vague unease into a specific, actionable conversation — and that's the whole point of having shared language.
{{< /protip >}}

## Design patterns

**Design patterns** are proven, reusable solutions to common problems in software design. They aren't copy-paste blueprints; they're templates for structuring code to solve a recurring type of problem in a maintainable, flexible way.

The classic set comes from the **Gang of Four** (GoF) — four authors who cataloged 23 object-oriented design patterns in their 1994 book, [*Design Patterns*](https://www.amazon.com/Design-Patterns-Elements-Reusable-Object-Oriented/dp/0201633612). ([More on the Gang of Four.](https://en.wikipedia.org/wiki/Design_Patterns#The_%22Gang_of_Four%22))

Why they're worth knowing:

- **Consistency** — Teams share a common pattern language.
- **Maintainability** — They encapsulate varying behavior and simplify future change.
- **Scalability** — They encourage loosely-coupled, well-organized code.
- **Best practices** — They package decades of collective experience.

The patterns fall into three categories: **Creational** (object creation), **Structural** (object composition), and **Behavioral** (object interaction).

**Creational**

| Pattern | What it does | Example |
|---|---|---|
| [Singleton](https://refactoring.guru/design-patterns/singleton) | Ensure only one instance exists and is globally accessible. | A single `Logger` instance. |
| [Factory Method](https://refactoring.guru/design-patterns/factory-method) | Let subclasses decide which class to instantiate. | Creating MySQL or PostgreSQL connections based on config. |
| [Abstract Factory](https://refactoring.guru/design-patterns/abstract-factory) | Create families of related objects without naming concrete classes. | A `UIFactory` producing Windows or Mac controls. |
| [Builder](https://refactoring.guru/design-patterns/builder) | Construct a complex object step by step. | Building a `House` with optional features. |
| [Prototype](https://refactoring.guru/design-patterns/prototype) | Create new objects by cloning a prototype instance. | Duplicating a document template. |

**Structural**

| Pattern | What it does | Example |
|---|---|---|
| [Adapter](https://refactoring.guru/design-patterns/adapter) | Convert one interface into another clients expect. | Wrapping a legacy payment API to match your interface. |
| [Composite](https://refactoring.guru/design-patterns/composite) | Treat individual objects and compositions uniformly. | File-system directories and files in a tree. |
| [Proxy](https://refactoring.guru/design-patterns/proxy) | Control access to an object or defer its initialization. | Lazy-loading a high-resolution image. |
| [Decorator](https://refactoring.guru/design-patterns/decorator) | Add responsibilities to objects dynamically. | Adding encryption to a data stream. |
| [Bridge](https://refactoring.guru/design-patterns/bridge) | Decouple an abstraction from its implementation. | Separating UI code from platform-specific drawing APIs. |

**Behavioral**

| Pattern | What it does | Example |
|---|---|---|
| [Strategy](https://refactoring.guru/design-patterns/strategy) | Encapsulate interchangeable algorithms. | Selecting quicksort or mergesort at runtime. |
| [Observer](https://refactoring.guru/design-patterns/observer) | Notify dependent objects when state changes. | UI components updating when data models change. |
| [Command](https://refactoring.guru/design-patterns/command) | Encapsulate a request as an object to queue it or support undo. | Undo/redo in a text editor. |
| [State](https://refactoring.guru/design-patterns/state) | Alter behavior when an object's internal state changes. | A vending machine switching between "Waiting" and "Dispensing." |
| [Template Method](https://refactoring.guru/design-patterns/template-method) | Define the skeleton of an algorithm, deferring steps to subclasses. | A test framework's setup/teardown sequence. |

For a complete, illustrated catalog of the classic patterns and more, see [Refactoring.Guru — Design Patterns Catalog](https://refactoring.guru/design-patterns/catalog).
