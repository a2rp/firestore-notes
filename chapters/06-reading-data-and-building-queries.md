# 6. Reading data and building queries

[Back to notes index](../README.md)

| Previous | Notes index | Next |
| --- | --- | --- |
| [Previous: Create, update, delete, and write patterns](./05-create-update-delete-and-write-patterns.md) | [Notes index](../README.md) | [Next: Query indexes, pagination, and listeners](./07-query-indexes-pagination-and-listeners.md) |

## Read one document

Use getDoc when the application has a document reference and needs a one-time read.

~~~js
import { doc, getDoc } from "firebase/firestore";

const taskRef = doc(db, "tasks", "task-789");
const snapshot = await getDoc(taskRef);

if (snapshot.exists()) {
  const task = snapshot.data();
  renderTask(task);
} else {
  renderMissingTask();
}
~~~

The reference identifies a path, while the DocumentSnapshot contains the result of a read. Always check exists before treating data as a record. A document may have been deleted since the app last showed it.

## Read a collection or query

Use getDocs to execute a collection reference or a query and receive a QuerySnapshot.

~~~js
import {
  collection,
  getDocs,
  limit,
  orderBy,
  query,
  where,
} from "firebase/firestore";

const tasksQuery = query(
  collection(db, "tasks"),
  where("ownerId", "==", currentUser.uid),
  where("done", "==", false),
  orderBy("createdAt", "desc"),
  limit(20)
);

const snapshot = await getDocs(tasksQuery);
const tasks = snapshot.docs.map((document) => ({
  id: document.id,
  ...document.data(),
}));

renderTasks(tasks);
~~~

This query asks for the signed-in user's unfinished tasks, newest first, with a maximum of 20 results. A matching composite index may be needed. If the console provides an index link after a query error, review the requested fields and create the index in the correct project.

## Use filters intentionally

The where function adds a condition to a query. Common conditions include equality, range comparisons, array membership, and membership in a supplied set. Combine filters to narrow the data at the database.

~~~js
const publicPosts = query(
  collection(db, "posts"),
  where("visibility", "==", "public"),
  where("publishedAt", "<=", new Date()),
  orderBy("publishedAt", "desc"),
  limit(10)
);
~~~

Choose fields and values that are present and consistently typed. For example, compare timestamps with timestamp-compatible values and store a boolean as a boolean. A query can omit documents that lack a field used for ordering.

Queries have limits on combinations of filters and values. Those limits can change with database edition and product updates. Check the current query documentation when a query uses several OR conditions, inequality fields, or a large list of values.

## Keep result sets bounded

Do not download an entire growing collection to display a small list. Add a limit and load more with cursors when needed. This reduces data transfer, rendering work, and reads.

Do not read data in a loop when one query can return the needed records. A screen that reads one document for every row can create many network requests. Consider a suitable query, a small denormalized field, or a separate summary when that matches the data model.

## Handle empty and failed reads

A query with no matching documents still succeeds. Check the snapshot's empty property or map over docs, and show a useful empty state.

A failed read can mean the network is unavailable, the user is not authorized, or the query is not supported by its index. Keep these cases out of the same UI state where possible, and do not treat a permission-denied error as an empty result.

## Know whether you need a snapshot listener

getDoc and getDocs return a one-time snapshot. If the app needs later changes, use onSnapshot, which is covered in the next chapter. Do not use a live listener by default for data that can be loaded once.

## Practice questions

1. Which function reads one document once?
2. What does getDocs return?
3. Why should a query have a limit?
4. What can happen when a document lacks a field used by orderBy?
5. When does a query need a composite index?
6. Should an empty query result be shown as an error?
7. When is a one-time get preferable to a live listener?
8. What should the UI do when a read is denied?

## Main references

- [Read data with the JavaScript SDK](https://firebase.google.com/docs/firestore/query-data/get-data)
- [Perform simple and compound queries](https://firebase.google.com/docs/firestore/query-data/queries)
- [Order and limit data](https://firebase.google.com/docs/firestore/query-data/order-limit-data)
- [Firestore index overview](https://firebase.google.com/docs/firestore/query-data/index-overview)
