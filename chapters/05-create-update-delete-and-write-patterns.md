# 5. Create, update, delete, and write patterns

[Back to notes index](../README.md)

| Previous | Notes index | Next |
| --- | --- | --- |
| [Previous: Data types, field values, and references](./04-data-types-field-values-and-references.md) | [Notes index](../README.md) | [Next: Reading data and building queries](./06-reading-data-and-building-queries.md) |

## Create a document with an automatic ID

Use addDoc when the database should generate a new document ID. The returned reference contains the assigned ID.

~~~js
import { addDoc, collection, serverTimestamp } from "firebase/firestore";
import { db } from "./firebase.js";

const taskRef = await addDoc(collection(db, "tasks"), {
  title: "Review the write patterns",
  done: false,
  ownerId: "user-456",
  createdAt: serverTimestamp(),
});

console.log(taskRef.id);
~~~

Use an automatic ID when each new submission should create a distinct record. If a user retries after a slow response, the retry can create a duplicate. For operations that must be safe to retry, use a stable ID or a separate idempotency design.

## Write to a known document ID

Use setDoc when the application knows the document path. Without options, setDoc replaces the stored fields with the provided document data.

~~~js
import { doc, serverTimestamp, setDoc } from "firebase/firestore";

const profileRef = doc(db, "users", "user-456");

await setDoc(profileRef, {
  displayName: "Ash",
  createdAt: serverTimestamp(),
});
~~~

Use a merge option when a write should create the document if it is missing and update only the supplied fields if it exists.

~~~js
await setDoc(
  profileRef,
  {
    displayName: "Ash",
    updatedAt: serverTimestamp(),
  },
  { merge: true }
);
~~~

Merge is convenient for partial records, but it does not automatically validate the shape of your data. Check field names, types, ownership, and allowed changes in Security Rules or trusted server code.

## Update selected fields

Use updateDoc when the document is expected to exist and only selected fields should change. It fails if the document is missing.

~~~js
import { doc, updateDoc } from "firebase/firestore";

await updateDoc(doc(db, "tasks", "task-789"), {
  done: true,
  "settings.pinned": true,
});
~~~

The dot-separated field path changes one nested field. Providing the complete map under settings instead would replace that map value. Use the form that matches the change the user made.

## Change arrays and counters safely

Field transforms let the backend apply a change against the latest stored value.

~~~js
import {
  arrayRemove,
  arrayUnion,
  doc,
  increment,
  updateDoc,
} from "firebase/firestore";

const taskRef = doc(db, "tasks", "task-789");

await updateDoc(taskRef, {
  labels: arrayUnion("reviewed"),
  viewCount: increment(1),
});

await updateDoc(taskRef, {
  labels: arrayRemove("draft"),
});
~~~

A transform does not make a business rule safe by itself. For example, a client should not be allowed to increment a private billing amount simply because increment is atomic. Rules must restrict which fields the client can change.

## Remove a field or a document

Use deleteField to remove one field. Use deleteDoc to remove the document.

~~~js
import { deleteDoc, deleteField, doc, updateDoc } from "firebase/firestore";

const taskRef = doc(db, "tasks", "task-789");

await updateDoc(taskRef, {
  temporaryNote: deleteField(),
});

await deleteDoc(taskRef);
~~~

Deleting a parent document does not recursively delete its subcollections. If a record has nested documents or an associated Storage object, implement and test the related cleanup separately.

## Handle write failures

Writes are asynchronous and can fail because of network problems, invalid data, a missing document, or a denied Security Rules request. Show a useful state to the user and preserve the form values when retrying.

~~~js
try {
  await updateDoc(taskRef, {
    done: true,
    updatedAt: serverTimestamp(),
  });
  showSavedMessage();
} catch (error) {
  showSaveError(error);
}
~~~

Avoid displaying internal error details or sensitive paths to end users. Log enough non-sensitive context to diagnose the operation, and make sure any retry does not accidentally create duplicate records.

## Practice questions

1. When should addDoc be used?
2. What happens when setDoc writes without merge options?
3. When is setDoc with merge useful?
4. What happens when updateDoc targets a missing document?
5. How does a dotted field path differ from replacing the entire map?
6. Which transform can add one to a numeric field?
7. Does deleteDoc remove subcollection documents automatically?
8. What should an app do when a write is denied by Security Rules?

## Main references

- [Add and update data](https://firebase.google.com/docs/firestore/manage-data/add-data)
- [Delete data](https://firebase.google.com/docs/firestore/manage-data/delete-data)
- [Server timestamps](https://firebase.google.com/docs/firestore/manage-data/add-data#server_timestamp)
- [Field transforms](https://firebase.google.com/docs/firestore/manage-data/add-data#update_fields_in_nested_objects)
