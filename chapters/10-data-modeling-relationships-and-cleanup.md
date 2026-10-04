# 10. Data modeling, relationships, and cleanup

[Back to notes index](../README.md)

| Previous | Notes index | Next |
| --- | --- | --- |
| [Previous: Security Rules, authentication, and validation](./09-security-rules-authentication-and-validation.md) | [Notes index](../README.md) | [Next: Offline data, cache, and conflicts](./11-offline-data-cache-and-conflicts.md) |

## Design from screens and queries

Firestore does not join collections like a relational database. Begin with the screens and operations the app must support:

- Which records appear together on each screen?
- Which records are read by one user, one team, or everyone?
- Which filters and sort orders does the app need?
- Which changes must happen together?
- Which fields are allowed to become slightly out of date?

Write a sample query for each important screen before settling the data structure. A model that makes common reads simple is often better than one that minimizes every repeated field.

## Choose nested or top-level records

Put records in a subcollection when they belong to a parent, grow independently, and are usually read through that parent.

~~~text
users/user-456/tasks/task-789
~~~

This path works well for a signed-in user's private tasks. Rules can compare the user ID in the path with the authenticated UID.

Use a top-level collection when records need to be queried across many parents, or have a lifecycle independent from their parent.

~~~text
tasks/task-789
  ownerId: user-456
  teamId: team-123
~~~

A top-level collection can support a team task list, but each query and rule needs the fields and indexes that access pattern requires. There is no automatically correct choice. Pick one that matches the expected reads and permissions.

## Model one-to-many relationships

A small, fixed group of values can be stored as an array or map on the parent document. A large or growing set of child records should usually use a subcollection, because each child can be fetched, paged, and secured independently.

Do not put an unbounded list of comments or activity events into one document. The document grows as the list grows, and every write updates the same document.

## Decide what to duplicate

Denormalization means storing a small copy of a value in another document so a common screen can read fewer records. For example, a task row might include the project name so a list can render without an extra project read.

~~~text
projects/project-123
  name: "Study notes"

projects/project-123/tasks/task-789
  title: "Review indexes"
  projectName: "Study notes"
~~~

The project document is the authoritative source for the name in this example. If it changes, decide whether the app updates every task copy, accepts a short period of stale labels, or reads the project separately. A copied value without an update policy becomes inconsistent data.

## Store references with a read plan

A reference field points to another document, but it does not perform a join. If a screen needs the referenced record, the app must read it separately.

For list screens, consider whether a few copied display fields are cheaper and simpler than many individual reads. For sensitive values or rapidly changing data, avoid copying data into documents that may remain visible after access is removed.

## Plan deletion explicitly

Deleting a document does not cascade into its subcollections. Before deleting a parent, identify child collections, related top-level documents, Storage objects, and any external records that need cleanup.

Large recursive cleanup should use a trusted backend process rather than a web client. Make the cleanup resumable and observable. If the work spans many records, record its progress and make each step safe to retry.

Soft deletion can help support restore or audit workflows. Store an explicit state and define which queries exclude deleted records, how long the data remains, and who can permanently remove it.

## Practice questions

1. Why should the data model start from screen queries?
2. When is a subcollection a good fit?
3. When might a top-level collection be easier to query?
4. Why is an unbounded comments array a poor model?
5. When can duplicating a small field reduce reads?
6. What update decision must follow denormalization?
7. Does deleting a parent document remove nested records?
8. Why should large recursive cleanup run in trusted backend code?

## Main references

- [Choose a data structure](https://firebase.google.com/docs/firestore/manage-data/structure-data)
- [Data model and document paths](https://firebase.google.com/docs/firestore/data-model)
- [Delete data](https://firebase.google.com/docs/firestore/manage-data/delete-data)
- [Query subcollections with collection group queries](https://firebase.google.com/docs/firestore/query-data/queries#collection-group-query)
