[> Home](README.md) | [> Freebies](freebies.md)

# ADR-044 Use an Event-Driven Distributed Architecture

## Status
Decided, 2023-10-04  
Supersedes [ADR-031 Use serverless functions](https://link-to-superseded-ADR)

## Context
The Polyglot Media system is currently a distributed system consisting mostly of serverless functions. Moving from a monolith to a distributed serverless architecture was done to tackle problems with the responsiveness of the live system and a long lead time between functionality and bug fixes being coded and deployed to production.

Serverless architecture has not solved the problems with responsiveness or maintainability to the degree required, with functions being tightly coupled with many other functions.

A solution to the problems of responsiveness and time-to-market must be found.

## Evaluation Criteria
See [ADR-002 Select Architecture Characteristics](https://link-to-ADR-002)

- _Responsiveness_: Customers have been complaining about the responsiveness of the system, and this must be addressed. This is deemed the most important criterion.
- _Maintainability_: The serverless functions improved the time-to-market of bug fixes and new functionality but not to the extent needed.
- _Deployability_: Alongside maintainability, deployability is important to improve time-to-market for bug fixes and new functionality.
- _Scalability_: Most complaints from customers about responsiveness have been around peak use times, so the system must handle peak numbers of users without impacting users.

## Options
### 1. Microservices

| Criteria        | Score            | Rationale                                                                                                                                                                     |
| --------------- | ---------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Responsiveness  | ★★★☆☆ 3/5        | [Not inherently performant](https://link-to-reference-info) but optimisations can be put in place at bottlenecks, e.g. scaling                                                |
| Maintainability | ★★★☆☆ 3/5        | Dependencies can be an issue, many data stores to maintain                                                                                                                    |
| Deployability   | ★★★★★ 5/5        | Deploy only what has changed                                                                                                                                                  |
| Scalability     | ★★★★★ 5/5        | Scale on per-service basis                                                                                                                                                    |
|                 | **Total:** 16/20 | **Other Trade-offs**                                                                                                                                                          |
|                 |                  | - Must split data into [one data store per service](https://link-to-reference-info) <br/>- Costs are usually high for building <br/>- Complex and is hard to create workflows |

### 2. Service-based

| Criteria        | Score            | Rationale                                                                                                                      |
| --------------- | ---------------- | ------------------------------------------------------------------------------------------------------------------------------ |
| Responsiveness  | ★★★☆☆ 3/5        | [Not inherently performant](https://link-to-reference-info) but optimisations can be put in place at bottlenecks, e.g. scaling |
| Maintainability | ★★★★☆ 4/5        | Fewer data stores to maintain than microservices                                                                               |
| Deployability   | ★★★★☆ 4/5        | Deploy only what has changed, make sure changes to shared data store(s) don't affect other services                            |
| Scalability     | ★★★☆☆ 3/5        | Scale individual services, harder to scale shared data store(s) unless using [caching](https://link-to-reference-info)         |
|                 | **Total:** 14/20 | **Other Trade-offs**                                                                                                           |
|                 |                  | - Reasonably complex & hard to create workflows <br/>- Not as evolvable as microservices                                       |

### 3. Event-driven

| Criteria        | Score            | Rationale                                                                                                                    |
| --------------- | ---------------- | ---------------------------------------------------------------------------------------------------------------------------- |
| Responsiveness  | ★★★★★ 5/5        | Generally [fire-and-forget](https://link-to-reference-info), event processing can be scaled/optimised                        |
| Maintainability | ★★★★☆ 4/5        | Must manage event processing as well as services and data stores, events decouple services to be managed by individual teams |
| Deployability   | ★★★☆☆ 3/5        | Manage event processing deployment and changes as well as services                                                           |
| Scalability     | ★★★★★ 5/5        | Services and message processing can both be scaled                                                                           |
|                 | **Total:** 17/20 | **Other Trade-offs**                                                                                                         |
|                 |                  | - [Integration testing can be harder](https://link-to-reference-info)                                                        |

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

## Consultation
- _Vlad_: Neither microservices nor service-based are [inherently performant](https://link-to-reference-info), so likely will not boost responsiveness as much as we want.
- _Nikki_: I have experience in event-driven architecture, using queues. We monitored the length of queues and spun up extra instances of services to process long queues when needed.
- _Libby_: Queues and channels can be used to prioritise the processing of events, for example with the [ambulance pattern](https://link-to-reference-info).
- _Mark_: Responsiveness needs to be the top priority due to customer complaints.
- _Libby_: Pure microservices would require that services do not share data stores. Splitting our data like this would require a lot of effort.

LICENSE: [CC BY 4.0 (Jacqui Read / jacquiread.com)](https://creativecommons.org/licenses/by/4.0/).

[> Home](README.md) | [> Freebies](freebies.md)