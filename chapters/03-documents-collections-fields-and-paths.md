# 3. Documents, collections, fields, and paths

[Back to notes index](../README.md)

| Previous | Notes index | Next |
| --- | --- | --- |
| [Previous: JavaScript SDK and project configuration](./02-javascript-sdk-and-project-configuration.md) | [Notes index](../README.md) | [Next: Data types, field values, and references](./04-data-types-field-values-and-references.md) |

## Read a Firestore path

Firestore paths alternate between collection names and document IDs. A collection path has an odd number of segments. A document path has an even number.

~~~text
Collection path: projects
Document path:   projects/project-123
Collection path: projects/project-123/tasks
Document path:   projects/project-123/tasks/task-789
~~~

A collection contains documents. Documents contain fields, and a document may have subcollections. A subcollection is not a field inside the parent document. It is a separate collection at a longer path.

Document IDs are strings and are unique only within their collection. Two different collections can contain a document with the same ID. Paths are case-sensitive, so Projects and projects are different collection names.

## Build references with the SDK

A DocumentReference points to one document. A CollectionReference points to a collection and can be used to create a query. Creating a reference does not read data from the server.

~~~js
import { collection, doc } from "firebase/firestore";
import { db } from "./firebase.js";

const projectsRef = collection(db, "projects");
const projectRef = doc(db, "projects", "project-123");
const tasksRef = collection(db, "projects", "project-123", "tasks");
const taskRef = doc(
  db,
  "projects",
  "project-123",
  "tasks",
  "task-789"
);
~~~

The SDK helpers take path segments and construct the correct reference type. Use them instead of manually joining strings, especially when a path includes a user ID or a document ID.

A user-owned structure could use a subcollection:

~~~text
users/user-456
users/user-456/tasks/task-789
~~~

The path makes ownership easy to understand, but the path alone does not enforce authorization. Security Rules must check that the signed-in user's UID matches the user ID in the path.

## Choose document IDs

Firestore can generate an ID when adding a document to a collection. Use an auto ID when the app does not need to choose a meaningful identifier.

~~~js
import { addDoc, collection } from "firebase/firestore";

const taskRef = await addDoc(collection(db, "tasks"), {
  title: "Review Firestore paths",
  done: false,
});

console.log(taskRef.id);
~~~

Use a stable ID when the same logical item must be addressed predictably, such as one profile document per Auth UID or an idempotent record from a trusted external system. A stable ID makes retries easier to reason about, but a client must not be allowed to claim another user's ID.

Avoid putting private information in IDs. IDs can appear in logs, URLs, and client-visible references. A document's path is an identifier, not an access control rule.

## Keep document fields purposeful

A document holds fields as key-value pairs. Fields can include strings, numbers, booleans, timestamps, arrays, maps, and document references. A field can be absent, but queries and UI code should handle optional data intentionally.

~~~json
{
  "title": "Review Firestore paths",
  "done": false,
  "ownerId": "user-456",
  "labels": ["database", "notes"],
  "settings": {
    "pinned": true,
    "color": "orange"
  }
}
~~~

A map groups related values within a document. Use it for a small bounded object that is normally read and changed with its parent. Use a subcollection for a growing set of separately queried records.

Keep documents focused on the data that screens commonly read together. Repeating a small label can make list reads simpler, but decide which copy is authoritative and how updates reach other copies.

## Query a subcollection or matching subcollections

A collection query reads documents from one collection path. A collection group query can search every collection with the same collection ID, including subcollections in different parents.

For example, querying the tasks subcollection under one user only searches that user's tasks. A collection group query for tasks can search matching subcollections across users. Collection group queries require suitable indexes and Rules that cover the query path.

Use a collection group query only when the app needs cross-parent results. Put fields such as ownerId or status on each task when the query and access model need them.

## Avoid confusing a reference with its data

A DocumentReference points to a path. It does not contain the document fields and does not fetch the referenced document automatically. Call getDoc or attach a listener to read its data.

Store a reference when a relationship should point to another Firestore document. Store an ID or a small copied field when that is easier to query and maintain. Do not assume Firestore joins these records for you.

## Practice questions

1. How many path segments identify a collection, and how many identify a document?
2. Is a subcollection stored as a field inside its parent document?
3. Are document IDs unique across the entire Firestore database?
4. What does a DocumentReference contain?
5. When is an auto-generated document ID a good choice?
6. What does a collection group query search?
7. Does placing a user ID in a document path secure that document by itself?
8. When is a map preferable to a subcollection?

## Main references

- [Firestore data model](https://firebase.google.com/docs/firestore/data-model)
- [Structure data](https://firebase.google.com/docs/firestore/manage-data/structure-data)
- [Add data with the JavaScript SDK](https://firebase.google.com/docs/firestore/manage-data/add-data)
- [Query data with collection group queries](https://firebase.google.com/docs/firestore/query-data/queries#collection-group-query)
