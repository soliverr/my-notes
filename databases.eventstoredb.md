---
id: lowlrrwpduqx6rir6rub576
title: Eventstoredb
desc: ''
updated: 1658900024149
created: 1658899559586
---

[EventStoreDB](https://www.eventstore.com/eventstoredb) is an industrial-strength [Event Sourcing](https://www.eventstore.com/event-sourcing) database that stores your critical data in streams of immutable events. It was built from the ground up for Event Sourcing and offers an unrivaled solution for building event-sourced systems.

The core features such as guaranteed writes, concurrency model, granular stream and stream APIs make EventStoreDB the best choice for event-sourced systems - especially when compared with other database solutions originally built for other purposes,

## Event Sourcing and CQRS

* https://www.eventstore.com/blog/event-sourcing-and-cqrs
* https://martinfowler.com/bliki/ReportingDatabase.html

**CQRS** stands for __Command-Query Segregation Principle__. Greg Young [described](https://cqrs.files.wordpress.com/2010/11/cqrs_documents.pdf) (and named) the pattern thoroughly in 2010, but the idea existed way before that time. On a high level, CQRS states the fact that operations that trigger state transitions should be described as __commands__ and any data retrieval that goes beyond the need of the command execution, should be named a __query__. Because the operational requirements for executing commands and queries are very often different, developers should consider using different persistence techniques for handling commands and queries, therefore segregating them.ler.com/bliki/ReportingDatabase.html