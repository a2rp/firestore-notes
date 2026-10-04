# 11. Offline data, cache, and conflicts

[Back to notes index](../README.md)

| Previous | Notes index | Next |
| --- | --- | --- |
| [Previous: Data modeling, relationships, and cleanup](./10-data-modeling-relationships-and-cleanup.md) | [Notes index](../README.md) | [Next: Aggregation and summary data](./12-aggregation-and-summary-data.md) |

## Know what the local cache does

Firestore clients keep local data so the app can respond quickly and continue some work when the network is unavailable. A read can come from the server or from cached data, depending on connectivity and the read method.

When connectivity returns, local changes are synchronized with the backend. Multiple offline changes to the same document use last-write-wins behavior. This is not a merge policy for business data, so choose a model that can tolerate or explicitly handle conflicts.

For the web, the default local cache is memory-only. Persistent IndexedDB cache can be enabled during Firestore initialization. It can keep records available after a reload, which is useful for some applications but may be inappropriate on a shared device or for sensitive data.

## Enable persistent cache deliberately

Choose the cache before creating other Firestore references. This example configures persistence across multiple browser tabs.

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

If persistence is not needed, initialize Firestore with its default memory cache. Do not call getFirestore first and then attempt to change the cache. Initialization settings are chosen when the Firestore instance is created.

A persistent cache can retain data between sessions. If the app handles private user data, consider who can access the device, how sign-out works, and whether persistent cache is appropriate. Clearing the visible UI state does not necessarily remove cached database records.

## Explain pending writes

Firestore can apply a client write locally before the server confirms it. The UI can feel responsive, but the write may still be pending or can be rejected by Security Rules.

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

Use hasPendingWrites to show a saving or not-yet-confirmed state. Use fromCache when the screen needs to explain that displayed data may be stale. Always handle listener errors, including permission failures.

A batched write can be queued offline and synchronized later. A transaction needs to read the current server state and fails while offline. Show the user an honest result rather than claiming a transaction succeeded without confirmation.

## Read while offline

An offline document read can return a cached document. A collection query can only use documents present in the local cache, so an empty offline result does not prove that the server collection is empty.

Design a useful offline screen around data the user has already opened or saved. Provide a refresh path when the network returns and avoid presenting an old cached value as a confirmed current server value.

## Handle competing updates

Last-write-wins can be acceptable for independent preferences where the newest full document is intended to replace earlier values. It is risky when multiple users edit different fields or when every action must be retained.

For concurrent activity, consider storing each event as a separate document, using a transaction for a conditional online update, or moving privileged conflict resolution to a trusted server. Do not place unrelated user changes into one large document if they will frequently overwrite one another.

## Practice questions

1. What does Firestore keep in its local cache?
2. Which cache does the Web SDK use by default?
3. Which initialization function configures persistent browser cache?
4. Why should persistent cache be a deliberate choice for private data?
5. What does hasPendingWrites report?
6. Does an empty offline query prove that no server documents exist?
7. How are multiple offline changes to the same document reconciled?
8. Why does a Firestore transaction fail while the client is offline?

## Main references

- [Access data offline](https://firebase.google.com/docs/firestore/manage-data/enable-offline)
- [Firestore JavaScript cache settings](https://firebase.google.com/docs/reference/js/firestore.persistentcachesettings)
- [Snapshot metadata](https://firebase.google.com/docs/firestore/query-data/listen#events_for_metadata_changes)
- [Transactions and batched writes](https://firebase.google.com/docs/firestore/manage-data/transactions)
