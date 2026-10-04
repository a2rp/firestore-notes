# 7. Query indexes, pagination, and listeners

[Back to notes index](../README.md)

| Previous | Notes index | Next |
| --- | --- | --- |
| [Previous: Reading data and building queries](./06-reading-data-and-building-queries.md) | [Notes index](../README.md) | [Next: Transactions, batches, and concurrency](./08-transactions-batches-and-concurrency.md) |

## Understand why indexes exist

Firestore serves queries from indexes. It maintains many single-field indexes automatically. A query that combines fields or sort directions may need a composite index.

When a query needs an index, Firestore usually returns an error with a link to create it. Check the fields and sort order in the suggested index, then create it in the project that serves the application. Indexes take time to build before the query can run.

Do not add indexes for every possible field combination. Create the indexes used by real queries and disable indexing for large fields that are never filtered or ordered, following current Firestore guidance.

## Load the first page

Pair orderBy with limit to read a bounded page. Choose a sort field that every result has, and use a deterministic tie-breaker when records may share the same timestamp or score.

~~~js
import {
  collection,
  getDocs,
  limit,
  orderBy,
  query,
} from "firebase/firestore";

const firstPageQuery = query(
  collection(db, "tasks"),
  orderBy("createdAt", "desc"),
  orderBy("title"),
  limit(20)
);

const firstPage = await getDocs(firstPageQuery);
const lastVisible = firstPage.docs.at(-1);
~~~

The query must have an index that supports its filters and order clauses. The additional title ordering makes records with equal createdAt values appear in a consistent order.

## Fetch the next page with a cursor

Keep the final document snapshot from the current page and pass it to startAfter for the next page.

~~~js
import { getDocs, limit, query, startAfter } from "firebase/firestore";

async function loadNextPage(lastVisible) {
  if (!lastVisible) return [];

  const nextPageQuery = query(
    collection(db, "tasks"),
    orderBy("createdAt", "desc"),
    orderBy("title"),
    startAfter(lastVisible),
    limit(20)
  );

  const snapshot = await getDocs(nextPageQuery);
  return snapshot.docs;
}
~~~

A snapshot cursor carries the ordered values and document position. Keep the same filters and ordering between page requests. Do not use an offset to skip many records; cursors avoid reading and discarding earlier results.

If records change while the user pages, later pages can reflect the current database state. Decide whether the screen needs a live feed or a stable snapshot of a result set.

## Listen for changes

Use onSnapshot only when the interface should update as documents change.

~~~js
import { collection, onSnapshot, query, where } from "firebase/firestore";

const tasksQuery = query(
  collection(db, "tasks"),
  where("ownerId", "==", currentUser.uid)
);

const unsubscribe = onSnapshot(
  tasksQuery,
  (snapshot) => {
    const tasks = snapshot.docs.map((document) => ({
      id: document.id,
      ...document.data(),
    }));
    renderTasks(tasks);
  },
  (error) => {
    showReadError(error);
  }
);
~~~

The callback runs first with the current query result and again as the result changes. A listener can use reads and network resources for as long as it remains active. Unsubscribe when the view is removed or the user changes.

~~~js
unsubscribe();
~~~

In a component, store the returned function and call it from the cleanup path. If a signed-in user changes, stop the old user's listener before starting a new query.

## Distinguish cache state from server state

A snapshot can include metadata about whether the data came from cache and whether local writes are pending. The UI can use that information when it needs to label offline or unsynced data.

Do not interpret a cached result as proof that the server has accepted the write. Keep error and pending states visible where they matter to the user.

## Practice questions

1. Why does Firestore need indexes to serve queries?
2. When is a composite index required?
3. Why should the sort order include a stable tie-breaker?
4. What should be kept from the first page for cursor pagination?
5. Why is startAfter preferred to skipping many documents with an offset?
6. When should onSnapshot be used?
7. When should a listener be unsubscribed?
8. What does cached snapshot metadata tell the application?

## Main references

- [Firestore index overview](https://firebase.google.com/docs/firestore/query-data/index-overview)
- [Manage indexes](https://firebase.google.com/docs/firestore/query-data/indexing)
- [Paginate data with query cursors](https://firebase.google.com/docs/firestore/query-data/query-cursors)
- [Listen to real-time updates](https://firebase.google.com/docs/firestore/query-data/listen)
- [Snapshot metadata](https://firebase.google.com/docs/firestore/query-data/listen#events_for_metadata_changes)
