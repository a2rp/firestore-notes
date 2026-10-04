# 4. Data types, field values, and references

[Back to notes index](../README.md)

| Previous | Notes index | Next |
| --- | --- | --- |
| [Previous: Documents, collections, fields, and paths](./03-documents-collections-fields-and-paths.md) | [Notes index](../README.md) | [Next: Create, update, delete, and write patterns](./05-create-update-delete-and-write-patterns.md) |

## Choose a type that matches the meaning

Firestore stores common application values such as strings, numbers, booleans, null values, timestamps, arrays, maps, document references, geographic points, and byte data. Keep each field type consistent across documents that share a collection.

~~~json
{
  "title": "Read the data model chapter",
  "done": false,
  "priority": 2,
  "dueAt": "timestamp value",
  "labels": ["database", "study"],
  "settings": {
    "pinned": true
  },
  "ownerRef": "reference to users/user-456",
  "optionalNote": null
}
~~~

The example marks the timestamp and reference as descriptions. In a real write, provide a JavaScript Date, a Firestore Timestamp, or a DocumentReference. Do not store explanatory strings in place of typed values.

Keep dates as timestamps when the value represents a moment that should be sorted or compared by time. Store a formatted date string only when the text itself is the value, such as a label intended for display.

## Use server time for record history

The user's device clock may be inaccurate. Use serverTimestamp when a field should represent when Firestore accepted a write.

~~~js
import { doc, serverTimestamp, setDoc } from "firebase/firestore";
import { db } from "./firebase.js";

const taskRef = doc(db, "tasks", "task-789");

await setDoc(taskRef, {
  title: "Read the data model chapter",
  createdAt: serverTimestamp(),
  updatedAt: serverTimestamp(),
});
~~~

When reading a timestamp, the SDK exposes a Timestamp value with conversion methods such as toDate. Keep the stored timestamp for sorting and comparisons. Convert it to a JavaScript Date or a display string at the user interface boundary.

## Understand null, missing fields, and undefined

A missing field is not the same as a field set to null. A missing field has no value in the document. A null field is present and explicitly says that no value is set.

This distinction affects queries and validation. A filter for a field value does not automatically match documents where that field is absent. Decide whether the app needs a missing field, a null field, or a defined default.

Do not place JavaScript undefined values in document data. Omit fields that should be absent, or use null when explicit emptiness is part of the data model. SDK settings can affect how undefined values are handled, so do not rely on implicit behavior.

## Keep arrays and maps small and purposeful

An array is useful for a small, bounded list that is read with its document. Use arrayUnion or arrayRemove when you need to add or remove known elements without replacing the entire array.

A map keeps a small nested object within one document. Dot-separated field paths can update one nested field without replacing the whole map.

~~~js
import { doc, updateDoc } from "firebase/firestore";

await updateDoc(doc(db, "tasks", "task-789"), {
  "settings.pinned": true,
});
~~~

A growing list of independently queried items usually belongs in a subcollection. Arrays and maps remain part of their parent document and count toward its size and indexing behavior. Review the current limits and indexing settings before storing large or complex values.

## Store references when relationships need them

A DocumentReference is a typed pointer to a document path. It is useful when a field should identify another Firestore document without copying that document's full data.

~~~js
import { doc, setDoc } from "firebase/firestore";

const ownerRef = doc(db, "users", "user-456");
const taskRef = doc(db, "tasks", "task-789");

await setDoc(taskRef, {
  title: "Read the data model chapter",
  ownerRef,
});
~~~

A reference does not automatically fetch the target document and does not create a relational join. Read the referenced document explicitly when the screen needs its fields. A string ID may be simpler when queries, serialization, or relationships span systems.

## Apply field transforms

Field transforms let the server apply a change to the existing value. Use them for timestamps, counters, and small set-like array changes.

~~~js
import {
  arrayUnion,
  doc,
  increment,
  serverTimestamp,
  updateDoc,
} from "firebase/firestore";

await updateDoc(doc(db, "tasks", "task-789"), {
  updatedAt: serverTimestamp(),
  viewCount: increment(1),
  labels: arrayUnion("database"),
});
~~~

An update fails when its target document does not exist. Transforms are not substitutes for authorization or input validation. Security Rules must still restrict which users can apply the write and which field changes are allowed.

## Practice questions

1. Which Firestore type should represent a moment used for sorting?
2. Why use serverTimestamp instead of the device clock for createdAt?
3. How does a missing field differ from null?
4. Why should undefined values be omitted from writes?
5. When is an array appropriate for a document?
6. Does a DocumentReference load its target document automatically?
7. What does increment change?
8. What should be used when a growing list needs independent queries?

## Main references

- [Supported Firestore data types](https://firebase.google.com/docs/firestore/manage-data/data-types)
- [Add and update data](https://firebase.google.com/docs/firestore/manage-data/add-data)
- [JavaScript Timestamp reference](https://firebase.google.com/docs/reference/js/firestore_.timestamp)
- [Firestore document and field limits](https://firebase.google.com/docs/firestore/quotas)
