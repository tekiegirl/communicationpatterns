[> Home](README.md) | [> Freebies](freebies.md)

# ADR-044 Change to Event-Driven Architecture

## Status
Decided, 2023-10-04  
Supersedes [ADR-031 Use serverless functions](https://link-to-superseded-ADR)

## Context
The Polyglot Media system is currently a distributed system consisting mostly of serverless functions. Moving from a monolith to a distributed serverless architecture was done to tackle problems with the responsiveness of the live system and a long lead time between functionality and bug fixes being coded and deployed to production.

Serverless architecture has not solved the problems with responsiveness or maintainability to the degree required, with functions being tightly coupled with many other functions.

A solution to the problems of responsiveness and time-to-market must be found.

## Decision
We will use event-driven architecture due to its responsiveness and scalability, and the limited tradeoffs compared to the other two options. Event-driven also scores the highest of the three options on the most important criterion: responsiveness.

## Implications
### Positive
- We gain overall higher responsiveness, maintainability, and scalability than the current serverless functions.
- Processing of events is scalable by spinning up additional instances of services to process queued events.
- Processing within services is scalable by spinning up additional instances of services to handle the need for processing within the services.

### Negative
- Teams or individuals may need to learn new skills or technologies (such as event queues).
- Event management (such as queues) adds extra complexity to development and deployment.
- Integration testing and DevOps will need to be overhauled.

LICENSE: [CC BY 4.0 (Jacqui Read / jacquiread.com)](https://creativecommons.org/licenses/by/4.0/).

[> Home](README.md) | [> Freebies](freebies.md)