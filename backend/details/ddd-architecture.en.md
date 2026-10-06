[Русский](ddd-architecture.md) | English

# Architecture

DDD does not prescribe a specific architecture. Clean Architecture, event-driven architecture, CQRS, REST, and other approaches can be used.

**Vaughn Vernon gives the following advice in his book:**

> Use architectural approaches where they reduce a specific risk and where omitting them increases the likelihood of project failure or system failure.

This advice applies regardless of the development style you follow.

DDD does impose some constraints on architectural choices:

- **Domain models and their business logic should not depend on anything else.** This may conflict with approaches such as traditional n-layered architecture.
- **Domain models should be rich rather than anemic.**