# 14. Emulator, Rules testing, and debugging

[Back to notes index](../README.md)

| Previous | Notes index | Next |
| --- | --- | --- |
| [Previous: Import, export, backups, and retention](./13-import-export-backups-and-retention.md) | [Notes index](../README.md) | [Next: Performance, cost, and scalability](./15-performance-cost-and-scalability.md) |

## Run the Firestore emulator

The Local Emulator Suite runs a local Firestore service for development and testing. It lets you exercise reads, writes, and Security Rules without sending those requests to a production database.

Configure the Firestore rules file and emulator in firebase.json:

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

Start the Firestore emulator from the project directory:

~~~bash
firebase emulators:start --only firestore
~~~

The emulator uses the project ID supplied to the CLI or the project configuration. Use one consistent ID for the CLI, client app, and test environment.

## Connect the web SDK locally

Connect before sending any reads or writes. Keep the emulator connection behind a development-only condition so production builds always use the hosted service.

~~~js
import { connectFirestoreEmulator } from "firebase/firestore";
import { db } from "./firebase.js";

if (import.meta.env.DEV) {
  connectFirestoreEmulator(db, "127.0.0.1", 8080);
}
~~~

This condition uses Vite's development flag. Use the equivalent build-time flag if the app uses another build tool. Do not let a user-controlled browser value choose whether a production client connects to an emulator.

The Emulator Suite interface can show data, requests, and Security Rules evaluation details. Inspect the request path, authenticated identity, and rule expression that allowed or denied it.

## Test Rules with authenticated users

The Rules unit testing library creates client contexts with chosen authentication state. Install it as a development dependency and start the Firestore emulator before the test process.

~~~bash
npm install --save-dev @firebase/rules-unit-testing
firebase emulators:exec --only firestore "npm test"
~~~

A focused test can check that an owner is allowed and another user is denied:

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

Each test should assert the expected behavior, including failures. Use separate contexts for signed-in owners, other signed-in users, and unauthenticated requests. Clear test data between cases so one test does not affect another.

## Never test with accidental open Rules

If the Firestore emulator has no configured rules file and tests do not load Rules explicitly, it can behave as though access is open. That can make a test pass while the production configuration denies access.

Make the source of Rules explicit in firebase.json or initializeTestEnvironment. Verify the test output shows the expected allow and deny cases. Keep the emulator and demo project ID in local or CI test settings, not in a production deployment target.

## Debug a denied request systematically

When a client request fails, check these items in order:

1. Confirm the app points to the intended project and database.
2. Confirm the emulator or hosted database is the expected destination.
3. Read the exact document path and query constraints.
4. Confirm the Firebase Auth user and UID used by the request.
5. Check whether every field accessed by the rule exists and has the expected type.
6. Check whether the query can return only documents allowed by the rule.
7. Review the emulator Rules trace and run the denied case as a test.

Do not respond to a permission error by opening every path. Find the failed condition and add only the intended permission.

## Practice questions

1. Which service does the Firestore emulator run locally?
2. How does the web SDK connect to port 8080?
3. Why should emulator connection be limited to development?
4. Which library provides mocked authentication contexts for Rules tests?
5. What does assertFails check?
6. Why must the test environment load the intended Rules file?
7. Which identities should an owner-based Rules test include?
8. What should be checked before changing a denied request into an allowed one?

## Main references

- [Connect your app to the Firestore emulator](https://firebase.google.com/docs/emulator-suite/connect_firestore)
- [Install and configure the Emulator Suite](https://firebase.google.com/docs/emulator-suite/install_and_configure)
- [Test Firestore Security Rules](https://firebase.google.com/docs/firestore/security/test-rules-emulator)
- [Rules unit testing reference](https://firebase.google.com/docs/reference/emulator-suite/rules-unit-testing/rules-unit-testing)
