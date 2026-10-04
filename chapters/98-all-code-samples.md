# 98. All code samples

[Back to notes index](../README.md)

| Previous | Notes index | Next |
| --- | --- | --- |
| [Previous: Production operations and release checklist](./16-production-operations-and-release-checklist.md) | [Notes index](../README.md) | [Next: Complete questions and answers](./99-complete-q-and-a.md) |

This chapter collects every fenced example from the 16 core chapters. Each sample links back to the chapter that explains its use.

## Chapter 1: Firestore fundamentals and database setup

[Open the source chapter](./01-firestore-fundamentals-and-database-setup.md)

### Sample 1: Understand the database hierarchy

[Source: 01-firestore-fundamentals-and-database-setup.md](./01-firestore-fundamentals-and-database-setup.md)

~~~text
Firebase project
└── Cloud Firestore database
    └── collection: projects
        └── document: project-123
            ├── name: "Notes workspace"
            ├── ownerId: "user-456"
            └── collection: tasks
                └── document: task-789
                    ├── title: "Review query indexes"
                    └── done: false
~~~

### Sample 2: First project exercise

[Source: 01-firestore-fundamentals-and-database-setup.md](./01-firestore-fundamentals-and-database-setup.md)

~~~json
{
  "name": "Notes workspace",
  "ownerId": "demo-user",
  "createdAt": "server timestamp",
  "archived": false
}
~~~

## Chapter 2: JavaScript SDK and project configuration

[Open the source chapter](./02-javascript-sdk-and-project-configuration.md)

### Sample 1: Add the Firebase JavaScript SDK

[Source: 02-javascript-sdk-and-project-configuration.md](./02-javascript-sdk-and-project-configuration.md)

~~~bash
npm install firebase
~~~

### Sample 2: Register the web app

[Source: 02-javascript-sdk-and-project-configuration.md](./02-javascript-sdk-and-project-configuration.md)

~~~js
// src/firebase.js
import { initializeApp } from "firebase/app";
import { getFirestore } from "firebase/firestore";

const firebaseConfig = {
  apiKey: import.meta.env.VITE_FIREBASE_API_KEY,
  authDomain: import.meta.env.VITE_FIREBASE_AUTH_DOMAIN,
  projectId: import.meta.env.VITE_FIREBASE_PROJECT_ID,
  storageBucket: import.meta.env.VITE_FIREBASE_STORAGE_BUCKET,
  messagingSenderId: import.meta.env.VITE_FIREBASE_MESSAGING_SENDER_ID,
  appId: import.meta.env.VITE_FIREBASE_APP_ID,
};

const app = initializeApp(firebaseConfig);

export const db = getFirestore(app);
~~~

### Sample 3: Select an environment

[Source: 02-javascript-sdk-and-project-configuration.md](./02-javascript-sdk-and-project-configuration.md)

~~~text
VITE_FIREBASE_API_KEY=public-client-key
VITE_FIREBASE_AUTH_DOMAIN=sample-dev.firebaseapp.com
VITE_FIREBASE_PROJECT_ID=sample-dev
VITE_FIREBASE_STORAGE_BUCKET=sample-dev.appspot.com
VITE_FIREBASE_MESSAGING_SENDER_ID=1234567890
VITE_FIREBASE_APP_ID=1:1234567890:web:abcdef123456
~~~

### Sample 4: Avoid duplicate app initialization

[Source: 02-javascript-sdk-and-project-configuration.md](./02-javascript-sdk-and-project-configuration.md)

~~~js
import { getApp, getApps, initializeApp } from "firebase/app";

const app =
  getApps().length > 0
    ? getApp()
    : initializeApp(firebaseConfig);
~~~

## Chapter 3: Documents, collections, fields, and paths

[Open the source chapter](./03-documents-collections-fields-and-paths.md)

### Sample 1: Read a Firestore path

[Source: 03-documents-collections-fields-and-paths.md](./03-documents-collections-fields-and-paths.md)

~~~text
Collection path: projects
Document path:   projects/project-123
Collection path: projects/project-123/tasks
Document path:   projects/project-123/tasks/task-789
~~~

### Sample 2: Build references with the SDK

[Source: 03-documents-collections-fields-and-paths.md](./03-documents-collections-fields-and-paths.md)

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

### Sample 3: Build references with the SDK

[Source: 03-documents-collections-fields-and-paths.md](./03-documents-collections-fields-and-paths.md)

~~~text
users/user-456
users/user-456/tasks/task-789
~~~

### Sample 4: Choose document IDs

[Source: 03-documents-collections-fields-and-paths.md](./03-documents-collections-fields-and-paths.md)

~~~js
import { addDoc, collection } from "firebase/firestore";

const taskRef = await addDoc(collection(db, "tasks"), {
  title: "Review Firestore paths",
  done: false,
});

console.log(taskRef.id);
~~~

### Sample 5: Keep document fields purposeful

[Source: 03-documents-collections-fields-and-paths.md](./03-documents-collections-fields-and-paths.md)

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

## Chapter 4: Data types, field values, and references

[Open the source chapter](./04-data-types-field-values-and-references.md)

### Sample 1: Choose a type that matches the meaning

[Source: 04-data-types-field-values-and-references.md](./04-data-types-field-values-and-references.md)

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

### Sample 2: Use server time for record history

[Source: 04-data-types-field-values-and-references.md](./04-data-types-field-values-and-references.md)

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

### Sample 3: Keep arrays and maps small and purposeful

[Source: 04-data-types-field-values-and-references.md](./04-data-types-field-values-and-references.md)

~~~js
import { doc, updateDoc } from "firebase/firestore";

await updateDoc(doc(db, "tasks", "task-789"), {
  "settings.pinned": true,
});
~~~

### Sample 4: Store references when relationships need them

[Source: 04-data-types-field-values-and-references.md](./04-data-types-field-values-and-references.md)

~~~js
import { doc, setDoc } from "firebase/firestore";

const ownerRef = doc(db, "users", "user-456");
const taskRef = doc(db, "tasks", "task-789");

await setDoc(taskRef, {
  title: "Read the data model chapter",
  ownerRef,
});
~~~

### Sample 5: Apply field transforms

[Source: 04-data-types-field-values-and-references.md](./04-data-types-field-values-and-references.md)

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

## Chapter 5: Create, update, delete, and write patterns

[Open the source chapter](./05-create-update-delete-and-write-patterns.md)

### Sample 1: Create a document with an automatic ID

[Source: 05-create-update-delete-and-write-patterns.md](./05-create-update-delete-and-write-patterns.md)

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

### Sample 2: Write to a known document ID

[Source: 05-create-update-delete-and-write-patterns.md](./05-create-update-delete-and-write-patterns.md)

~~~js
import { doc, serverTimestamp, setDoc } from "firebase/firestore";

const profileRef = doc(db, "users", "user-456");

await setDoc(profileRef, {
  displayName: "Ash",
  createdAt: serverTimestamp(),
});
~~~

### Sample 3: Write to a known document ID

[Source: 05-create-update-delete-and-write-patterns.md](./05-create-update-delete-and-write-patterns.md)

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

### Sample 4: Update selected fields

[Source: 05-create-update-delete-and-write-patterns.md](./05-create-update-delete-and-write-patterns.md)

~~~js
import { doc, updateDoc } from "firebase/firestore";

await updateDoc(doc(db, "tasks", "task-789"), {
  done: true,
  "settings.pinned": true,
});
~~~

### Sample 5: Change arrays and counters safely

[Source: 05-create-update-delete-and-write-patterns.md](./05-create-update-delete-and-write-patterns.md)

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

### Sample 6: Remove a field or a document

[Source: 05-create-update-delete-and-write-patterns.md](./05-create-update-delete-and-write-patterns.md)

~~~js
import { deleteDoc, deleteField, doc, updateDoc } from "firebase/firestore";

const taskRef = doc(db, "tasks", "task-789");

await updateDoc(taskRef, {
  temporaryNote: deleteField(),
});

await deleteDoc(taskRef);
~~~

### Sample 7: Handle write failures

[Source: 05-create-update-delete-and-write-patterns.md](./05-create-update-delete-and-write-patterns.md)

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

## Chapter 6: Reading data and building queries

[Open the source chapter](./06-reading-data-and-building-queries.md)

### Sample 1: Read one document

[Source: 06-reading-data-and-building-queries.md](./06-reading-data-and-building-queries.md)

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

### Sample 2: Read a collection or query

[Source: 06-reading-data-and-building-queries.md](./06-reading-data-and-building-queries.md)

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

### Sample 3: Use filters intentionally

[Source: 06-reading-data-and-building-queries.md](./06-reading-data-and-building-queries.md)

~~~js
const publicPosts = query(
  collection(db, "posts"),
  where("visibility", "==", "public"),
  where("publishedAt", "<=", new Date()),
  orderBy("publishedAt", "desc"),
  limit(10)
);
~~~

## Chapter 7: Query indexes, pagination, and listeners

[Open the source chapter](./07-query-indexes-pagination-and-listeners.md)

### Sample 1: Load the first page

[Source: 07-query-indexes-pagination-and-listeners.md](./07-query-indexes-pagination-and-listeners.md)

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

### Sample 2: Fetch the next page with a cursor

[Source: 07-query-indexes-pagination-and-listeners.md](./07-query-indexes-pagination-and-listeners.md)

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

### Sample 3: Listen for changes

[Source: 07-query-indexes-pagination-and-listeners.md](./07-query-indexes-pagination-and-listeners.md)

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

### Sample 4: Listen for changes

[Source: 07-query-indexes-pagination-and-listeners.md](./07-query-indexes-pagination-and-listeners.md)

~~~js
unsubscribe();
~~~

## Chapter 8: Transactions, batches, and concurrency

[Open the source chapter](./08-transactions-batches-and-concurrency.md)

### Sample 1: Protect a read-modify-write operation

[Source: 08-transactions-batches-and-concurrency.md](./08-transactions-batches-and-concurrency.md)

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

### Sample 2: Group known writes in a batch

[Source: 08-transactions-batches-and-concurrency.md](./08-transactions-batches-and-concurrency.md)

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

## Chapter 9: Security Rules, authentication, and validation

[Open the source chapter](./09-security-rules-authentication-and-validation.md)

### Sample 1: Start from deny and allow specific paths

[Source: 09-security-rules-authentication-and-validation.md](./09-security-rules-authentication-and-validation.md)

~~~text
rules_version = '2';

service cloud.firestore {
  match /databases/{database}/documents {
    function signedInAs(userId) {
      return request.auth != null
        && request.auth.uid == userId;
    }

    match /users/{userId}/tasks/{taskId} {
      allow read, delete: if signedInAs(userId);

      allow create: if signedInAs(userId)
        && request.resource.data.keys().hasAll([
          'title',
          'done',
          'ownerId'
        ])
        && request.resource.data.keys().hasOnly([
          'title',
          'done',
          'ownerId'
        ])
        && request.resource.data.ownerId == userId
        && request.resource.data.title is string
        && request.resource.data.title.size() > 0
        && request.resource.data.title.size() <= 120
        && request.resource.data.done is bool;

      allow update: if signedInAs(userId)
        && request.resource.data.ownerId == resource.data.ownerId
        && request.resource.data.ownerId == userId
        && request.resource.data.diff(resource.data)
          .affectedKeys()
          .hasOnly(['title', 'done']);
    }
  }
}
~~~

### Sample 2: Make queries compatible with Rules

[Source: 09-security-rules-authentication-and-validation.md](./09-security-rules-authentication-and-validation.md)

~~~js
const ownTasksQuery = query(
  collection(db, "tasks"),
  where("ownerId", "==", currentUser.uid)
);
~~~

## Chapter 10: Data modeling, relationships, and cleanup

[Open the source chapter](./10-data-modeling-relationships-and-cleanup.md)

### Sample 1: Choose nested or top-level records

[Source: 10-data-modeling-relationships-and-cleanup.md](./10-data-modeling-relationships-and-cleanup.md)

~~~text
users/user-456/tasks/task-789
~~~

### Sample 2: Choose nested or top-level records

[Source: 10-data-modeling-relationships-and-cleanup.md](./10-data-modeling-relationships-and-cleanup.md)

~~~text
tasks/task-789
  ownerId: user-456
  teamId: team-123
~~~

### Sample 3: Decide what to duplicate

[Source: 10-data-modeling-relationships-and-cleanup.md](./10-data-modeling-relationships-and-cleanup.md)

~~~text
projects/project-123
  name: "Study notes"

projects/project-123/tasks/task-789
  title: "Review indexes"
  projectName: "Study notes"
~~~

## Chapter 11: Offline data, cache, and conflicts

[Open the source chapter](./11-offline-data-cache-and-conflicts.md)

### Sample 1: Enable persistent cache deliberately

[Source: 11-offline-data-cache-and-conflicts.md](./11-offline-data-cache-and-conflicts.md)

~~~js
import { initializeApp } from "firebase/app";
import {
  initializeFirestore,
  persistentLocalCache,
  persistentMultipleTabManager,
} from "firebase/firestore";

const app = initializeApp(firebaseConfig);

export const db = initializeFirestore(app, {
  localCache: persistentLocalCache({
    tabManager: persistentMultipleTabManager(),
  }),
});
~~~

### Sample 2: Explain pending writes

[Source: 11-offline-data-cache-and-conflicts.md](./11-offline-data-cache-and-conflicts.md)

~~~js
import { onSnapshot } from "firebase/firestore";

const unsubscribe = onSnapshot(
  taskRef,
  { includeMetadataChanges: true },
  (snapshot) => {
    if (!snapshot.exists()) return;

    renderTask(snapshot.data(), {
      pending: snapshot.metadata.hasPendingWrites,
      fromCache: snapshot.metadata.fromCache,
    });
  }
);
~~~

## Chapter 12: Aggregation and summary data

[Open the source chapter](./12-aggregation-and-summary-data.md)

### Sample 1: Ask for a summary instead of every document

[Source: 12-aggregation-and-summary-data.md](./12-aggregation-and-summary-data.md)

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

## Chapter 13: Import, export, backups, and retention

[Open the source chapter](./13-import-export-backups-and-retention.md)

### Sample 1: Export data with the managed service

[Source: 13-import-export-backups-and-retention.md](./13-import-export-backups-and-retention.md)

~~~bash
gcloud config set project sample-dev
gcloud firestore export gs://sample-firestore-backups/manual-check --database="(default)" --async
~~~

## Chapter 14: Emulator, Rules testing, and debugging

[Open the source chapter](./14-emulator-rules-testing-and-debugging.md)

### Sample 1: Run the Firestore emulator

[Source: 14-emulator-rules-testing-and-debugging.md](./14-emulator-rules-testing-and-debugging.md)

~~~json
{
  "firestore": {
    "rules": "firestore.rules"
  },
  "emulators": {
    "firestore": {
      "port": 8080
    },
    "ui": {
      "enabled": true
    }
  }
}
~~~

### Sample 2: Run the Firestore emulator

[Source: 14-emulator-rules-testing-and-debugging.md](./14-emulator-rules-testing-and-debugging.md)

~~~bash
firebase emulators:start --only firestore
~~~

### Sample 3: Connect the web SDK locally

[Source: 14-emulator-rules-testing-and-debugging.md](./14-emulator-rules-testing-and-debugging.md)

~~~js
import { connectFirestoreEmulator } from "firebase/firestore";
import { db } from "./firebase.js";

if (import.meta.env.DEV) {
  connectFirestoreEmulator(db, "127.0.0.1", 8080);
}
~~~

### Sample 4: Test Rules with authenticated users

[Source: 14-emulator-rules-testing-and-debugging.md](./14-emulator-rules-testing-and-debugging.md)

~~~bash
npm install --save-dev @firebase/rules-unit-testing
firebase emulators:exec --only firestore "npm test"
~~~

### Sample 5: Test Rules with authenticated users

[Source: 14-emulator-rules-testing-and-debugging.md](./14-emulator-rules-testing-and-debugging.md)

~~~js
import { readFileSync } from "node:fs";
import {
  assertFails,
  assertSucceeds,
  initializeTestEnvironment,
} from "@firebase/rules-unit-testing";
import { doc, getDoc, setDoc } from "firebase/firestore";

const testEnv = await initializeTestEnvironment({
  projectId: "demo-firestore",
  firestore: {
    rules: readFileSync("firestore.rules", "utf8"),
  },
});

const alice = testEnv.authenticatedContext("alice");
const bob = testEnv.authenticatedContext("bob");

const aliceTask = doc(
  alice.firestore(),
  "users",
  "alice",
  "tasks",
  "task-1"
);

await assertSucceeds(
  setDoc(aliceTask, {
    title: "Check emulator rules",
    done: false,
    ownerId: "alice",
  })
);

await assertSucceeds(getDoc(aliceTask));

await assertFails(
  getDoc(doc(bob.firestore(), "users", "alice", "tasks", "task-1"))
);

await testEnv.cleanup();
~~~

## Chapter 15: Performance, cost, and scalability

[Open the source chapter](./15-performance-cost-and-scalability.md)

### Sample 1: Make reads specific and bounded

[Source: 15-performance-cost-and-scalability.md](./15-performance-cost-and-scalability.md)

~~~js
import {
  collection,
  getDocs,
  limit,
  orderBy,
  query,
  where,
} from "firebase/firestore";

const recentTasksQuery = query(
  collection(db, "tasks"),
  where("ownerId", "==", currentUser.uid),
  orderBy("createdAt", "desc"),
  limit(25)
);

const snapshot = await getDocs(recentTasksQuery);
const tasks = snapshot.docs.map((document) => ({
  id: document.id,
  ...document.data(),
}));
~~~

## Chapter 16: Production operations and release checklist

[Open the source chapter](./16-production-operations-and-release-checklist.md)

### Sample 1: Keep database configuration in source control

[Source: 16-production-operations-and-release-checklist.md](./16-production-operations-and-release-checklist.md)

~~~json
{
  "firestore": {
    "rules": "firestore.rules",
    "indexes": "firestore.indexes.json"
  }
}
~~~

### Sample 2: Keep database configuration in source control

[Source: 16-production-operations-and-release-checklist.md](./16-production-operations-and-release-checklist.md)

~~~bash
firebase deploy --only firestore:rules
firebase deploy --only firestore:indexes
~~~
