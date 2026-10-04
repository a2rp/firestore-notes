# 15. Performance, cost, and scalability

[Back to notes index](../README.md)

| Previous | Notes index | Next |
| --- | --- | --- |
| [Previous: Emulator, Rules testing, and debugging](./14-emulator-rules-testing-and-debugging.md) | [Notes index](../README.md) | [Next: Production operations and release checklist](./16-production-operations-and-release-checklist.md) |

## Measure the full cost of a feature

Firestore usage can include document reads, writes, and deletes, index entries read, stored data including index storage, and network transfer. A listener can incur reads as its result set changes. Rules that read dependent documents can also add usage.

Estimate the behavior of a screen before launch:

- How many documents does the first view load?
- How often does it refresh or keep a listener open?
- Does each row trigger extra document reads?
- Which indexes does the query scan?
- How much data does each document and response carry?
- How often does the feature write or delete records?

Check the current pricing for the database edition and location. Pricing, free usage, and product behavior can change. Use the Firebase and Google Cloud usage dashboards after launch to compare actual usage with the estimate.

## Make reads specific and bounded

Use filters, order clauses, and limits that match the screen. Paginate long lists and read documents together through one query when possible instead of issuing a request for each row.

Choose listeners for data that must update live. Stop them when their screen or user session ends. A listener on a broad, frequently changing collection can create unnecessary reads and network work.

Keep documents reasonably small and avoid returning fields that a screen does not need when an alternate data shape can reduce transfer. If a field is not used in queries or ordering, consider whether it needs to be indexed.

## Reduce unnecessary index work

Indexes support query execution but also use storage and are updated when documents change. Index fanout can contribute to write latency. Keep only the indexes the application needs, and use field exemptions for large text, arrays, maps, or timestamp fields that are never queried.

Let the console's missing-index link help create a needed composite index, then review it as part of the database configuration. Do not disable indexing on a field until you have checked all queries that rely on it.

## Avoid hot documents and narrow key ranges

Frequent writes to one document can contend because all writers update the same record. Split high-volume state across records when the product can safely combine it later. Distributed counters are one example.

Use automatically generated document IDs for high-rate creation instead of sequential IDs. Sequential indexed fields, such as timestamps, can also create hot index ranges under high write rates. If such a field is not queried, a single-field index exemption may help. Check current edition-specific scaling guidance before planning a high-volume workload.

Ramp traffic gradually when introducing a new high-volume collection. Watch latency and error rates while the database adapts to a new access pattern.

## Choose the right write tool

Transactions are useful for a conditional read and write, but should remain small and avoid repeated external side effects. Batches are useful for a bounded group of atomic writes.

For a large server-side import or backfill, use a supported bulk-writing approach rather than sending a huge sequence of client requests. Make jobs restartable, track progress, and prevent the same source record from being applied twice.

## Diagnose with query and usage tools

Use Query Explain with a supported server client library to inspect query planning and, when requested, measured execution information. Analyze results in a safe environment and account for the query's normal usage cost.

Track Firestore reads, writes, deletes, stored data, network traffic, request latency, and permission errors. A cost change after a release may come from a new listener, an unbounded query, a loop of per-row reads, or a write-heavy index design.

## Practice questions

1. Which kinds of Firestore usage can affect cost?
2. Why should a growing list use a bounded query and pagination?
3. How can broad real-time listeners increase usage?
4. What is index fanout?
5. Why can a single frequently updated document become a hotspot?
6. How can sequential document IDs or indexed timestamps affect high-rate writes?
7. Which tool can inspect server-side query planning?
8. What should be compared after a release to find a cost increase?

## Main references

- [Cloud Firestore best practices](https://firebase.google.com/docs/firestore/best-practices)
- [Firestore pricing](https://firebase.google.com/docs/firestore/pricing)
- [Understand reads and writes at scale](https://firebase.google.com/docs/firestore/understand-reads-writes-scale)
- [Query Explain](https://firebase.google.com/docs/firestore/query-explain)
- [Distributed counters](https://firebase.google.com/docs/firestore/solutions/counters)
