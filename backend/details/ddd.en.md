[Русский](ddd.md) | English

# Domain Driven Design

**DDD** is an approach to software development focused on understanding the business domain or individual business processes. It provides guidance at different levels, from writing code to establishing a shared domain language within the team.

In his book, Vaughn Vernon distinguishes three areas:

- **Strategic modeling** — understanding which domains the requirements involve, how they relate, and how the team communicates about them.
- **Architecture** — the most important decisions about organizing a software system. DDD does not prescribe a particular architecture; requirements guide the choice, although some approaches can conflict with its principles.
- **Tactical modeling** — organizing code: data types, methods, business logic, infrastructure logic, and related concerns.

Before studying the details, consider the strengths and weaknesses of this approach.

Advantages:

- A shared language between domain experts and developers makes requirements clearer and helps both sides understand the system.
- Clear functional boundaries allow teams to develop parts of the system more independently.
- Separating business logic makes it easier to test and modify. Domain experts can also read the code to identify logic errors or compare it with documentation.

Disadvantages:

- Requires experienced developers, especially when starting a project or introducing DDD.
- Adds complexity and infrastructure code.
- Direct access to domain experts, an important part of the approach, is not always available.

**The following sections describe the main approaches at each level:**

[Strategic modeling](ddd-strategic-modeling.en.md)

[Architecture](ddd-architecture.en.md)

[Tactical modeling](ddd-tactical-modeling.en.md)
