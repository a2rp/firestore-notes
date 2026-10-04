# 12. Aggregation and summary data

[Back to notes index](../README.md)

| Previous | Notes index | Next |
| --- | --- | --- |
| [Previous: Offline data, cache, and conflicts](./11-offline-data-cache-and-conflicts.md) | [Notes index](../README.md) | [Next: Import, export, backups, and retention](./13-import-export-backups-and-retention.md) |

## Ask for a summary instead of every document

A read-time aggregation returns a summary value without sending every matching document to the client. Firestore supports count, sum, and average aggregation queries for supported query shapes.

~~~js
import {
  average,
  collection,
  count,
  getAggregateFromServer,
  query,
  sum,
  where,
} from "firebase/firestore";

const paidOrders = query(
  collection(db, "orders"),
  where("ownerId", "==", currentUser.uid),
  where("status", "==", "paid")
);

const summary = await getAggregateFromServer(paidOrders, {
  orderCount: count(),
  totalAmount: sum("total"),
  averageAmount: average("total"),
});

const { orderCount, totalAmount, averageAmount } = summary.data();
~~~

The query is evaluated by the server and returns the aggregate values. It does not provide a real-time listener or use the offline cache. A cached or locally pending write is not included in the server aggregate until the backend has accepted it.

Sum and average operate on numeric values. Keep the queried fields present and consistently typed, and check how ordering or multiple aggregates affect which documents are included.

## Understand cost and response time

An aggregate avoids transferring every matching document, which can reduce response size and the number of individual document reads compared with downloading the full result and calculating it in JavaScript.

Aggregation queries scan matching index entries. Cost and latency therefore depend on the query and the number of index entries it examines. Review current pricing and query explain information before running a large aggregate frequently.

Use a bounded or narrower query when possible. An aggregate query is not a substitute for a carefully scoped data access rule, and the client request must still satisfy Security Rules.

## Store a summary for a live display

A maintained summary document can serve a dashboard that needs an immediately available total or live updates. A trusted backend function can update the summary when source records change.

This adds write-time work. The function must tolerate retries, avoid counting the same event twice, and define how edits or deletes change the summary. Make the source documents authoritative and treat the summary as derived data that can be rebuilt.

Do not update one popular counter document at a high rate without checking write contention. A distributed counter spreads increments over shard documents, then sums the shards when a total is needed. This improves write throughput at the cost of a more involved read and more documents.

## Choose read-time or write-time aggregation

| Need | Approach |
| --- | --- |
| Occasional total for a filtered query | Read-time aggregation |
| Current number shown in a live interface | Maintained summary or counter |
| High write volume for one popular counter | Distributed counter pattern |
| Financial or access-control decision | Trusted authoritative server logic |

A display total should not replace validation of a critical operation. If the app reserves inventory or computes a payment, perform the decision in trusted logic with a transaction or another suitable backend design.

## Practice questions

1. Which aggregation functions does Firestore provide?
2. Does an aggregate query return all matching documents?
3. Can a read-time aggregate use an offline cache or live listener?
4. What determines the cost and latency of a large aggregation?
5. When might a maintained summary document be useful?
6. Why must a write-time aggregation handle repeated events safely?
7. What is the purpose of a distributed counter?
8. Should a display total decide whether a payment or reservation is allowed?

## Main references

- [Summarize data with aggregation queries](https://firebase.google.com/docs/firestore/query-data/aggregation-queries)
- [Firestore pricing](https://firebase.google.com/docs/firestore/pricing)
- [Write-time aggregations](https://firebase.google.com/docs/firestore/solutions/aggregation)
- [Distributed counters](https://firebase.google.com/docs/firestore/solutions/counters)
