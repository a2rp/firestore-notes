# 9. Security Rules, authentication, and validation

[Back to notes index](../README.md)

| Previous | Notes index | Next |
| --- | --- | --- |
| [Previous: Transactions, batches, and concurrency](./08-transactions-batches-and-concurrency.md) | [Notes index](../README.md) | [Next: Data modeling, relationships, and cleanup](./10-data-modeling-relationships-and-cleanup.md) |

## Separate identity from permission

Firebase Authentication identifies a user. Firestore Security Rules decide whether a client request from that user may read or write a document.

A signed-in user is not automatically allowed to read every record. Rules should check both the identity and the requested path or document fields. A hidden button or route guard only changes the interface and does not protect database access.

## Start from deny and allow specific paths

Firestore Rules do not have a safe default open policy. Write explicit matches for each collection and operation, and avoid a broad allow rule that exposes every document.

This example lets a signed-in user read and delete tasks in their own nested collection. It also validates required fields on create and restricts changes on update.

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

Here, resource.data is the stored document before an update. request.resource.data is the proposed document after a create or update. The create rule checks that the document has only the expected fields and that their types and values are valid.

The update rule prevents a user from changing ownerId and allows changes only to title and done. Add any future fields deliberately, with their type, size, and allowed changes specified.

A rule on the user document does not automatically apply to the tasks subcollection. Each nested path needs a matching rule.

## Make queries compatible with Rules

Security Rules are not filters. Firestore does not return the allowed portion of a query. It checks whether the query could return only documents the user is permitted to read, and rejects the whole request if it cannot prove that.

For a top-level tasks collection, an owner rule generally requires the query to constrain ownerId to the signed-in UID.

~~~js
const ownTasksQuery = query(
  collection(db, "tasks"),
  where("ownerId", "==", currentUser.uid)
);
~~~

For the nested path in the Rules example, query that user's task collection path. Do not let the client select an arbitrary user ID and treat it as authorization. The Rules must still compare the path ID to request.auth.uid.

## Protect server access separately

Server Firestore libraries, REST, and RPC calls do not use mobile and web Security Rules in the same way as client SDK calls. Server libraries authenticate through Google Cloud IAM and bypass Firestore Rules.

Give the server identity only the IAM permissions it needs. In server request handlers, validate the signed-in identity and resource ownership before using privileged database access. Never trust an owner ID supplied only by the browser.

## Test both allowed and denied operations

For every rule, write down a small access table before launch:

| Request | Expected result |
| --- | --- |
| Signed-in owner reads their task | Allow |
| Another signed-in user reads the task | Deny |
| Signed-out client reads the task | Deny |
| Owner creates a valid task in their path | Allow |
| Owner tries to set a different ownerId | Deny |
| Owner writes an unexpected field or invalid title | Deny |

Use the Firestore emulator and Rules unit testing tools to verify these cases. Test queries as well as single-document reads. A console simulator is useful for quick checks, but automated tests help prevent a later rule edit from reopening access.

## Practice questions

1. What is the difference between authentication and authorization?
2. What do resource.data and request.resource.data represent?
3. Why do Firestore Rules not filter query results?
4. How does the example rule restrict a task to its owner?
5. Why should an update rule preserve ownerId?
6. Does a rule on a parent document cover its subcollections?
7. Which access system applies to server client libraries?
8. Name four allowed and denied cases to test for an owner-only task collection.

## Main references

- [Get started with Firestore Security Rules](https://firebase.google.com/docs/firestore/security/get-started)
- [Understand rules and queries](https://firebase.google.com/docs/firestore/security/rules-query)
- [Control access to specific fields](https://firebase.google.com/docs/firestore/security/rules-fields)
- [Test Rules with the Firestore emulator](https://firebase.google.com/docs/firestore/security/test-rules-emulator)
- [Server libraries and IAM](https://firebase.google.com/docs/firestore/security/rules-conditions#authentication)
