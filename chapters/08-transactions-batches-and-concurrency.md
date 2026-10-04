# 8. Transactions, batches, and concurrency

[Back to notes index](../README.md)

| Previous | Notes index | Next |
| --- | --- | --- |
| [Previous: Query indexes, pagination, and listeners](./07-query-indexes-pagination-and-listeners.md) | [Notes index](../README.md) | [Next: Security Rules, authentication, and validation](./09-security-rules-authentication-and-validation.md) |

## Choose the right atomic operation

A transaction reads one or more documents, decides what to write from their current values, and applies all writes together. If a document changes while the transaction runs, Firestore may retry the callback with fresh data.

A write batch groups writes that do not depend on reading the latest values. The operations commit atomically: either all succeed or none are applied.

Use a transaction for conditional read and update logic. Use a batch when several known writes should succeed or fail together and no read is needed to choose them.

## Protect a read-modify-write operation

Consider a counter that must not go below zero. Reading the value and updating it in two separate requests can lose updates when two clients act at the same time. A transaction reads the current count and checks the rule before writing.

~~~js
import { doc, runTransaction } from "firebase/firestore";

const stockRef = doc(db, "products", "product-123");

await runTransaction(db, async (transaction) => {
  const snapshot = await transaction.get(stockRef);

  if (!snapshot.exists()) {
    throw new Error("Product does not exist");
  }

  const stock = snapshot.data().stock;

  if (stock <= 0) {
    throw new Error("Product is out of stock");
  }

  transaction.update(stockRef, {
    stock: stock - 1,
  });
});
~~~

All transaction reads must happen before its writes. The callback can run more than once, so do not send a notification, update a screen, charge a payment, or perform any other external side effect inside it. Run those actions only after the transaction has completed successfully, with a separate idempotent workflow when needed.

Transactions require a connection to the backend. Handle offline or retry failures and explain the outcome to the user.

## Group known writes in a batch

Use a write batch when multiple independent documents should change together.

~~~js
import { doc, writeBatch } from "firebase/firestore";

const batch = writeBatch(db);

batch.update(doc(db, "tasks", "task-1"), {
  archived: true,
});

batch.update(doc(db, "tasks", "task-2"), {
  archived: true,
});

batch.set(doc(db, "archiveJobs", "job-1"), {
  status: "complete",
});

await batch.commit();
~~~

Each operation is still subject to Security Rules and contributes to database usage. Keep batches small enough for current product limits and rule evaluation. A batch is not a replacement for a server-side bulk import tool when many records must be written.

Batched writes can be queued while a client is offline and synchronized when connectivity returns. The interface should distinguish a locally pending change from a server-confirmed result when that distinction matters.

## Understand conflicts and retries

A transaction callback must be safe to repeat. It should compute proposed writes from the snapshots it reads and avoid changing variables outside the callback in a way that changes the result on a retry.

An atomic commit does not mean a business operation can never be duplicated. If a user repeats a checkout after a timeout, design an idempotency key or stable operation record so the server can recognize the same request.

For a simple counter increase that does not need a conditional check, increment can be simpler than a transaction. For inventory reservation, balance checks, or state transitions, the operation often needs a transaction or trusted server logic.

## Keep authorization in place

Atomic operations do not bypass access control. Security Rules evaluate writes from client SDKs, including writes in a batch or transaction. Server client libraries bypass Firestore Rules and must use IAM and server-side authorization.

Test both success and failure cases. For example, verify that a user can reserve an available item, two competing updates cannot both claim the final item, and a user cannot change another person's record.

## Practice questions

1. What does a Firestore transaction do?
2. When should a write batch be used instead of a transaction?
3. Why can a transaction callback run more than once?
4. Why must transaction reads come before writes?
5. Why should a transaction callback avoid sending notifications?
6. Can a batched write be queued while the client is offline?
7. Does atomicity make a repeated business request automatically idempotent?
8. Which access controls apply to client writes in a batch?

## Main references

- [Transactions and batched writes](https://firebase.google.com/docs/firestore/manage-data/transactions)
- [Field value transforms](https://firebase.google.com/docs/firestore/manage-data/add-data#update_fields_increment)
- [Firestore Security Rules](https://firebase.google.com/docs/firestore/security/get-started)
- [Bulk data operations](https://firebase.google.com/docs/firestore/manage-data/bulk-writes)
