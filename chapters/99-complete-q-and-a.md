# 99. Complete questions and answers

[Back to notes index](../README.md)

| Previous | Notes index | Next |
| --- | --- | --- |
| [Previous: All code samples](./98-all-code-samples.md) | [Notes index](../README.md) | End of notes |

This chapter answers the eight practice questions at the end of each of the 16 core chapters.

## Chapter 1: Firestore fundamentals and database setup

[Open the source chapter](./01-firestore-fundamentals-and-database-setup.md)

### 1. How is a document different from a collection?

**Answer:** A collection groups documents under one path. A document is one record with fields and may have its own subcollections.

### 2. Why is Firestore called a NoSQL document database?

**Answer:** It stores data as documents grouped into collections instead of rows in relational tables. Queries use supported indexes, and the data model should fit the app's access patterns.

### 3. What path identifies the task in the example hierarchy?

**Answer:** The task path is users/user-456/tasks/task-789. It contains an even number of path segments, so it identifies a document.

### 4. Does registering another web app create a separate database?

**Answer:** No. Registering another web app adds another client identity to the same Firebase project. The apps still use the project's shared backend resources.

### 5. Why should development and production data use separate projects?

**Answer:** Separate projects isolate data, configuration, access, and release risks. This reduces the chance that a development build reads or changes production records.

### 6. What does a Firestore web configuration protect?

**Answer:** The web configuration identifies the Firebase project and registered app for the client SDK. It does not control who can read or write database records.

### 7. Which authorization system applies to mobile and web client SDK requests?

**Answer:** Mobile and web client SDK requests are evaluated by Firestore Security Rules. Trusted server libraries use Google Cloud IAM and bypass those client rules.

### 8. What should be checked before choosing a production database location?

**Answer:** Consider user and compute locations, latency, legal requirements, supported services, and whether the database location can be changed later.

## Chapter 2: JavaScript SDK and project configuration

[Open the source chapter](./02-javascript-sdk-and-project-configuration.md)

### 1. Which npm package provides the Firebase JavaScript SDK?

**Answer:** Install the firebase package. The package manifest records the dependency and the lock file records the resolved versions.

### 2. Where should the Firebase app and Firestore instance be initialized?

**Answer:** Initialize the Firebase app and Firestore instance once in a shared module. Export the instances so feature files can reuse them.

### 3. Why should feature files reuse the shared Firestore instance?

**Answer:** One shared instance keeps feature modules on the same project and avoids conflicting initialization. It also makes configuration changes easier to review.

### 4. How should a build choose between development and production projects?

**Answer:** Use separate build configuration for each environment and verify the selected project ID before any production operation.

### 5. Why are Vite variables that begin with VITE_ public?

**Answer:** Vite includes VITE_ values in the browser build, where users can inspect them. They are public configuration, not server secrets.

### 6. Name two credentials that must not enter the browser bundle.

**Answer:** Service account private keys and Admin SDK credentials must remain in a protected server or deployment environment. Private API keys also must not be bundled into browser code.

### 7. What should be verified before the client writes its first record?

**Answer:** Verify the project ID, database, environment, and initial Security Rules. Start with fictional data and a safe read or write.

### 8. When is an existing-app guard useful?

**Answer:** It helps in development setups where hot reload or repeated module evaluation may initialize the shared module more than once. Most normal client apps should initialize the default app once.

## Chapter 3: Documents, collections, fields, and paths

[Open the source chapter](./03-documents-collections-fields-and-paths.md)

### 1. How many path segments identify a collection, and how many identify a document?

**Answer:** A collection path has an odd number of path segments, while a document path has an even number. For example, projects is a collection and projects/p1 is a document.

### 2. Is a subcollection stored as a field inside its parent document?

**Answer:** No. A subcollection is a separate collection at a longer path beneath a document. It can be queried separately from its parent.

### 3. Are document IDs unique across the entire Firestore database?

**Answer:** No. Document IDs are unique within their collection, not across the entire database. Different collections may use the same ID.

### 4. What does a DocumentReference contain?

**Answer:** A DocumentReference identifies a document path. It does not contain the document fields and does not fetch the document automatically.

### 5. When is an auto-generated document ID a good choice?

**Answer:** Use an auto ID when each new record should get a unique identifier and the app does not need to choose a stable key.

### 6. What does a collection group query search?

**Answer:** It searches every collection or subcollection with the same collection ID. The query can return matching documents from different parent paths.

### 7. Does placing a user ID in a document path secure that document by itself?

**Answer:** No. A path makes the ownership relationship clear, but Security Rules must compare the path user ID to the authenticated UID.

### 8. When is a map preferable to a subcollection?

**Answer:** A map fits a small bounded object read and changed with its parent. A growing list that needs separate queries should use a subcollection.

## Chapter 4: Data types, field values, and references

[Open the source chapter](./04-data-types-field-values-and-references.md)

### 1. Which Firestore type should represent a moment used for sorting?

**Answer:** Use a Firestore Timestamp or a JavaScript Date that the SDK serializes as a timestamp. This supports chronological ordering and comparisons.

### 2. Why use serverTimestamp instead of the device clock for createdAt?

**Answer:** The device clock may be inaccurate or changed by the user. serverTimestamp records the time accepted by Firestore.

### 3. How does a missing field differ from null?

**Answer:** A missing field is absent from the document. A null field is present and explicitly has no value.

### 4. Why should undefined values be omitted from writes?

**Answer:** Undefined is not a Firestore data type and the SDK rejects it by default. Omit the property or use null when explicit emptiness is intended.

### 5. When is an array appropriate for a document?

**Answer:** Use an array for a small, bounded list that is read together with the document. Move a growing, independently queried list into a subcollection.

### 6. Does a DocumentReference load its target document automatically?

**Answer:** No. It only points to a document path. The app must read the target explicitly.

### 7. What does increment change?

**Answer:** increment applies a numeric change to the latest stored field value on the backend. It avoids replacing the field with a value calculated from a stale client read.

### 8. What should be used when a growing list needs independent queries?

**Answer:** Use a subcollection so each child record can be independently queried, paged, updated, and secured.

## Chapter 5: Create, update, delete, and write patterns

[Open the source chapter](./05-create-update-delete-and-write-patterns.md)

### 1. When should addDoc be used?

**Answer:** Use addDoc when a new record should receive an automatically generated document ID. The returned reference provides that ID.

### 2. What happens when setDoc writes without merge options?

**Answer:** setDoc without merge replaces the document fields with the supplied data. Fields not included in the new value are removed.

### 3. When is setDoc with merge useful?

**Answer:** Merge creates a document if it is missing and updates only the supplied fields if it exists. Rules must still validate the resulting data.

### 4. What happens when updateDoc targets a missing document?

**Answer:** updateDoc fails because the target document does not exist. Use setDoc if creation is intended.

### 5. How does a dotted field path differ from replacing the entire map?

**Answer:** A dotted field path changes one nested field while leaving other map values intact. Passing a complete map can replace the map value.

### 6. Which transform can add one to a numeric field?

**Answer:** Use increment to add or subtract from a numeric field using a server-side transform.

### 7. Does deleteDoc remove subcollection documents automatically?

**Answer:** No. Deleting a document does not recursively remove subcollections or associated Storage objects.

### 8. What should an app do when a write is denied by Security Rules?

**Answer:** Show a useful failure state, keep the user's input where appropriate, and check the denied path and Rules. Do not make the database public to silence the error.

## Chapter 6: Reading data and building queries

[Open the source chapter](./06-reading-data-and-building-queries.md)

### 1. Which function reads one document once?

**Answer:** getDoc reads one document reference once. Check the returned snapshot with exists before using its data.

### 2. What does getDocs return?

**Answer:** getDocs returns a QuerySnapshot with the matching document snapshots and result metadata.

### 3. Why should a query have a limit?

**Answer:** A limit bounds returned documents, transfer, rendering work, and reads. It also gives the app a page size for cursor pagination.

### 4. What can happen when a document lacks a field used by orderBy?

**Answer:** The document is omitted from results for that orderBy query because the ordered field is missing. Keep the field consistent or change the query model.

### 5. When does a query need a composite index?

**Answer:** A composite index is needed when a query combines filters and ordering that a built-in single-field index cannot serve.

### 6. Should an empty query result be shown as an error?

**Answer:** No. An empty query is a successful result with no matching documents. Render an empty state rather than an error.

### 7. When is a one-time get preferable to a live listener?

**Answer:** Use a one-time read when the screen needs a snapshot and does not need live changes. This avoids keeping an unnecessary listener active.

### 8. What should the UI do when a read is denied?

**Answer:** Show an access error or sign-in action as appropriate. A permission error is not the same as an empty result.

## Chapter 7: Query indexes, pagination, and listeners

[Open the source chapter](./07-query-indexes-pagination-and-listeners.md)

### 1. Why does Firestore need indexes to serve queries?

**Answer:** Indexes let Firestore locate and order matching records efficiently. Each supported query must be served by a suitable index.

### 2. When is a composite index required?

**Answer:** A composite index is needed when a query combines multiple fields or sort orders that are not covered by automatic single-field indexes.

### 3. Why should the sort order include a stable tie-breaker?

**Answer:** If several documents share the main sort value, a stable second ordering keeps page boundaries deterministic and reduces repeated or skipped rows.

### 4. What should be kept from the first page for cursor pagination?

**Answer:** Keep the last document snapshot from the current page and pass it to startAfter for the next page.

### 5. Why is startAfter preferred to skipping many documents with an offset?

**Answer:** A cursor starts from the desired point in the index. An offset reads and discards earlier results, which adds work as the offset grows.

### 6. When should onSnapshot be used?

**Answer:** Use onSnapshot when the interface must receive updates while it is open. Use getDoc or getDocs for data that only needs a one-time read.

### 7. When should a listener be unsubscribed?

**Answer:** Unsubscribe when the view closes, the user changes, or the query is no longer needed. Otherwise the listener remains active and can keep using resources.

### 8. What does cached snapshot metadata tell the application?

**Answer:** fromCache reports whether a snapshot came from local cache. hasPendingWrites reports whether local changes have not yet been confirmed by the server.

## Chapter 8: Transactions, batches, and concurrency

[Open the source chapter](./08-transactions-batches-and-concurrency.md)

### 1. What does a Firestore transaction do?

**Answer:** A transaction reads documents and applies writes atomically based on the values it read. Firestore can retry it if a document changes concurrently.

### 2. When should a write batch be used instead of a transaction?

**Answer:** Use a batch for a known set of writes that do not depend on reading current values. Use a transaction when writes depend on current document contents.

### 3. Why can a transaction callback run more than once?

**Answer:** Firestore can rerun it after a concurrent change so the logic uses fresh data. The callback must therefore be safe to execute multiple times.

### 4. Why must transaction reads come before writes?

**Answer:** Firestore needs the current values before deciding its writes. Perform all transaction reads before issuing transaction writes.

### 5. Why should a transaction callback avoid sending notifications?

**Answer:** A retry can repeat the callback, which could send duplicate messages or perform another external effect. Run side effects after a successful commit and make them idempotent.

### 6. Can a batched write be queued while the client is offline?

**Answer:** Yes. Client batched writes can be queued offline and synchronized later. The UI should distinguish a pending local write from a server-confirmed result.

### 7. Does atomicity make a repeated business request automatically idempotent?

**Answer:** No. A repeated business request can still create duplicate work. Use a stable operation ID or idempotency record to recognize retries.

### 8. Which access controls apply to client writes in a batch?

**Answer:** Client batch writes are evaluated by Firestore Security Rules. Trusted server libraries bypass Rules and must use IAM plus server-side authorization.

## Chapter 9: Security Rules, authentication, and validation

[Open the source chapter](./09-security-rules-authentication-and-validation.md)

### 1. What is the difference between authentication and authorization?

**Answer:** Authentication establishes who is making a request. Authorization determines whether that identity may perform the requested operation.

### 2. What do resource.data and request.resource.data represent?

**Answer:** resource.data is the stored document before the write. request.resource.data is the proposed document after a create or update.

### 3. Why do Firestore Rules not filter query results?

**Answer:** Rules are all-or-nothing checks, not post-query filters. Firestore rejects a query that could return a document the user is not permitted to read.

### 4. How does the example rule restrict a task to its owner?

**Answer:** The rule requires an authenticated user and checks that request.auth.uid matches the user ID in the task path. It also validates ownerId and fields.

### 5. Why should an update rule preserve ownerId?

**Answer:** If the owner field could change, a user might transfer a record or claim another user's data. Keep the existing owner unchanged and match it to the path.

### 6. Does a rule on a parent document cover its subcollections?

**Answer:** No. Each subcollection path requires its own matching rule. A match on a parent document does not automatically include descendants.

### 7. Which access system applies to server client libraries?

**Answer:** Server libraries authenticate through Google Cloud IAM and bypass Firestore Security Rules. The server must check user and record authorization itself.

### 8. Name four allowed and denied cases to test for an owner-only task collection.

**Answer:** Test that an owner can read and update their task, another user cannot read or change it, and a signed-out request is denied. Also test invalid fields and owner changes.

## Chapter 10: Data modeling, relationships, and cleanup

[Open the source chapter](./10-data-modeling-relationships-and-cleanup.md)

### 1. Why should the data model start from screen queries?

**Answer:** The screen queries reveal which records are read together, filtered, and sorted. A model designed around those reads usually needs fewer extra requests.

### 2. When is a subcollection a good fit?

**Answer:** Use a subcollection when child records belong to a parent, grow independently, and are commonly queried through that parent.

### 3. When might a top-level collection be easier to query?

**Answer:** A top-level collection can make cross-parent queries easier. Include owner or team fields and matching Rules and indexes for those queries.

### 4. Why is an unbounded comments array a poor model?

**Answer:** An unbounded array grows the parent document and causes unrelated updates to write the same record. It is also difficult to page and secure child items separately.

### 5. When can duplicating a small field reduce reads?

**Answer:** Copying a small display field can let a list render without another read for every row.

### 6. What update decision must follow denormalization?

**Answer:** Choose an authoritative source and define how copies are updated when it changes. Without that policy, duplicated values become stale.

### 7. Does deleting a parent document remove nested records?

**Answer:** No. Subcollection documents remain after the parent is deleted. Cleanup must be planned separately.

### 8. Why should large recursive cleanup run in trusted backend code?

**Answer:** A trusted backend can resume and observe large cleanup jobs. A browser client is unreliable for recursively deleting large amounts of data and should not hold broad privileges.

## Chapter 11: Offline data, cache, and conflicts

[Open the source chapter](./11-offline-data-cache-and-conflicts.md)

### 1. What does Firestore keep in its local cache?

**Answer:** The client caches documents it has received and can use them for local reads and writes when offline. Cached data may be older than the server state.

### 2. Which cache does the Web SDK use by default?

**Answer:** The Web SDK uses memory-only cache by default when no local cache is configured.

### 3. Which initialization function configures persistent browser cache?

**Answer:** Use initializeFirestore with persistentLocalCache and the desired tab manager before creating other Firestore instances or references.

### 4. Why should persistent cache be a deliberate choice for private data?

**Answer:** Persistent cache can remain on the device after a session ends. A shared device could expose private records to another person using the same browser profile.

### 5. What does hasPendingWrites report?

**Answer:** It indicates whether the local document has writes that are not yet acknowledged by the server.

### 6. Does an empty offline query prove that no server documents exist?

**Answer:** No. Offline queries only see cached documents, so an empty local result does not prove the server has no matching documents.

### 7. How are multiple offline changes to the same document reconciled?

**Answer:** The client synchronizes local changes when online again, and multiple changes to the same document use last-write-wins behavior.

### 8. Why does a Firestore transaction fail while the client is offline?

**Answer:** A transaction needs the current backend values to safely evaluate its conditions. It cannot complete that read while disconnected.

## Chapter 12: Aggregation and summary data

[Open the source chapter](./12-aggregation-and-summary-data.md)

### 1. Which aggregation functions does Firestore provide?

**Answer:** Firestore supports count, sum, and average aggregation queries for supported query shapes.

### 2. Does an aggregate query return all matching documents?

**Answer:** No. Firestore computes and returns the summary values instead of sending every matching document to the client.

### 3. Can a read-time aggregate use an offline cache or live listener?

**Answer:** No. Read-time aggregation is served directly by the backend and does not support offline queries or real-time listeners.

### 4. What determines the cost and latency of a large aggregation?

**Answer:** Cost and latency depend on the matching index entries scanned and the query's index configuration. Review current pricing and query execution details.

### 5. When might a maintained summary document be useful?

**Answer:** A maintained summary is useful when an interface needs a current total or live updates without repeatedly running a large aggregate query.

### 6. Why must a write-time aggregation handle repeated events safely?

**Answer:** Backend events can be retried or delivered more than once. The aggregation must recognize duplicate work so a record is not counted twice.

### 7. What is the purpose of a distributed counter?

**Answer:** A distributed counter spreads frequent increments across multiple shard documents to reduce contention on one document.

### 8. Should a display total decide whether a payment or reservation is allowed?

**Answer:** No. A display total is derived data and should not authorize a payment or reservation. Make critical decisions in trusted logic.

## Chapter 13: Import, export, backups, and retention

[Open the source chapter](./13-import-export-backups-and-retention.md)

### 1. How does a backup differ from an export?

**Answer:** An export copies database data to Cloud Storage for migration, analysis, or recovery. A backup is a consistent point-in-time copy designed for restore.

### 2. Which service stores managed export files?

**Answer:** Cloud Storage holds the managed export files.

### 3. Why can an export include writes made while it is running?

**Answer:** The export runs over time, so database changes made during that operation may be reflected in its output. It is not guaranteed to represent the exact start time.

### 4. What should be checked before importing into a destination database?

**Answer:** Confirm the project and database, export prefix, destination access, existing-data effects, indexes, and the intended restore procedure.

### 5. Why should a restore be tested in an isolated database?

**Answer:** Isolation lets you confirm that the backup is usable without overwriting live records. It also reveals missing steps before an incident.

### 6. What is point-in-time recovery for?

**Answer:** PITR provides recovery to a recent point in time within the configured recovery window. Check current product settings and requirements.

### 7. Does TTL remove a document at the exact expiration second?

**Answer:** No. TTL deletes asynchronously, typically within 24 hours after expiration, rather than at an exact second.

### 8. Does deleting a document through TTL remove its subcollections?

**Answer:** No. TTL removes the targeted document but does not delete its subcollections.

## Chapter 14: Emulator, Rules testing, and debugging

[Open the source chapter](./14-emulator-rules-testing-and-debugging.md)

### 1. Which service does the Firestore emulator run locally?

**Answer:** It runs a local Firestore service for development and testing without sending requests to the hosted database.

### 2. How does the web SDK connect to port 8080?

**Answer:** Call connectFirestoreEmulator with the Firestore instance, 127.0.0.1, and port 8080 before any database request.

### 3. Why should emulator connection be limited to development?

**Answer:** A production client must use its hosted database. An emulator connection should be selected only by a development build, not a browser-controlled value.

### 4. Which library provides mocked authentication contexts for Rules tests?

**Answer:** The @firebase/rules-unit-testing package provides authenticated, unauthenticated, and Rules-disabled contexts for tests.

### 5. What does assertFails check?

**Answer:** assertFails verifies that a request is rejected by Security Rules. It fails the test if the request succeeds or fails for an unrelated reason.

### 6. Why must the test environment load the intended Rules file?

**Answer:** Without explicit Rules, the emulator can behave as though access is open. Tests could pass even though production denies or permits different operations.

### 7. Which identities should an owner-based Rules test include?

**Answer:** Include the owner, another signed-in user, and an unauthenticated client. Test allowed reads and writes as well as denied cases.

### 8. What should be checked before changing a denied request into an allowed one?

**Answer:** Find the exact failing path, identity, query, field type, and Rule condition. Allow only the intended request after testing both positive and negative cases.

## Chapter 15: Performance, cost, and scalability

[Open the source chapter](./15-performance-cost-and-scalability.md)

### 1. Which kinds of Firestore usage can affect cost?

**Answer:** Reads, writes, deletes, index entries, stored data, and network transfer can affect usage costs. Exact pricing varies by database edition and location.

### 2. Why should a growing list use a bounded query and pagination?

**Answer:** A bounded page limits the records returned and gives the client a controlled way to request more. This avoids downloading a growing collection all at once.

### 3. How can broad real-time listeners increase usage?

**Answer:** Listeners can read their initial results and later document changes. A broad or long-lived listener can keep processing updates the screen does not need.

### 4. What is index fanout?

**Answer:** Index fanout is the number of index entries that must be updated for a document write. More unnecessary index work can increase storage and write latency.

### 5. Why can a single frequently updated document become a hotspot?

**Answer:** All writes contend on the same document and cannot be distributed across separate key ranges. Split frequent updates across records when the model allows it.

### 6. How can sequential document IDs or indexed timestamps affect high-rate writes?

**Answer:** Sequential IDs concentrate writes in nearby key ranges. Sequential indexed fields can also create hot index ranges during high write rates.

### 7. Which tool can inspect server-side query planning?

**Answer:** Query Explain can show server-side planning and measured execution details through a supported server client library.

### 8. What should be compared after a release to find a cost increase?

**Answer:** Compare query volume, listeners, reads, writes, deletes, storage, transfer, latency, and errors before and after the release.

## Chapter 16: Production operations and release checklist

[Open the source chapter](./16-production-operations-and-release-checklist.md)

### 1. Why should production use a separate Firebase project?

**Answer:** A separate project keeps production records, settings, and access isolated from development data and reduces the risk of an accidental test change.

### 2. Where should Firestore Rules and indexes be stored?

**Answer:** Track firestore.rules, firestore.indexes.json, and their firebase.json configuration in the application repository so changes can be reviewed and tested.

### 3. What happens to console Rules when the CLI deploys the local Rules file?

**Answer:** The deployed local Rules replace the Rules currently published for that database. Bring console edits into the source-controlled file before deploying.

### 4. Why should indexes be ready before code depends on them?

**Answer:** The application query needs the index to run successfully. Creating and waiting for it before release avoids deploying code that cannot serve its required query.

### 5. How can a field migration support old and new app versions?

**Answer:** Add the new field first and let reads support both shapes, backfill existing documents, then move writes to the new shape. Remove the old field only after old clients no longer need it.

### 6. Does rolling back application code reverse database writes?

**Answer:** No. A code rollback changes application behavior but leaves accepted database writes in place. Data recovery needs its own safe plan.

### 7. Which access cases should be tested before release?

**Answer:** Test the owner, another user, and a signed-out user, including successful and denied reads and writes. Also validate the Rules and required indexes.

### 8. What should be checked after a successful deployment?

**Answer:** Verify key user flows, permission errors, latency, listener behavior, deployed Rules and indexes, usage, and the actual Firebase project.
