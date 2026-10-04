# 1. Firestore fundamentals and database setup

[Back to notes index](../README.md)

| Previous | Notes index | Next |
| --- | --- | --- |
| [Previous: Notes index](../README.md) | [Notes index](../README.md) | [Next: JavaScript SDK and project configuration](./02-javascript-sdk-and-project-configuration.md) |

## What Cloud Firestore does

Cloud Firestore is a hosted NoSQL database for application data. It stores records as documents, groups documents into collections, and lets applications read a document or query a collection. Client applications can also subscribe to changes and receive updated snapshots.

Firestore is useful for data that an app needs to address by record and query by selected fields. Examples include profiles, tasks, products, comments, and order summaries. It is not a relational database with joins. Plan the shape of records around the queries the app needs.

Firestore is one product in Firebase. Firebase Authentication can provide a signed-in identity, while Firestore Security Rules decide which database operations that identity can perform. The web app configuration only identifies the Firebase project. It does not keep the database private.

## Understand the database hierarchy

A Firebase project can contain one or more Firestore databases. A database contains collections, collections contain documents, and a document can contain nested fields and subcollections.

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

A collection is a group of documents with a shared path. A document is a single record with fields. A subcollection is a separate collection beneath a document path. It can be queried independently and can grow without making the parent document larger.

The path to the example project document is **projects/project-123**. The path to its task is **projects/project-123/tasks/task-789**. A document path has an even number of path segments. A collection path has an odd number.

## Know when Firestore fits

Firestore is a good fit when the app needs document reads, field-based queries, real-time updates, client SDKs, and Security Rules. Consider another data store when the feature depends on relational joins, complex ad hoc reporting, or a specialized search engine.

Firestore and Realtime Database both support real-time data, but their models differ. Firestore organizes records as documents and collections. Realtime Database stores one JSON tree and synchronizes values at paths. Choose based on the access patterns and data shape.

Firestore query results are built from indexes. You do not fetch arbitrary rows and filter them later in application memory. The query must describe the records you need, and the database must have the index required to serve it.

## Choose the project and database deliberately

Create separate Firebase projects for development and production when their data, access, or release risks should be isolated. A registered web app is a client identity inside the project. It does not create a separate database.

Before creating a database, decide:

- Which Firebase project and environment will own it.
- Which region is suitable for the users and the system that reads the data.
- Which services and client apps need access.
- Which data is public, private, or restricted to an owner.
- Whether the default database or another database is intended.
- How the database will be tested before real user data is stored.

Database location affects latency and may be difficult or impossible to change after creation. Check current product requirements, supported locations, and organization rules before making the production database. Keep development data separate from live customer data.

## Start with safe access

During setup, choose restrictive Security Rules and test them before the client writes real data. Avoid rules that allow every user to read and write every document. A rule such as **allow read, write: if true** is unsafe for a deployed database.

Mobile and web client SDK requests are evaluated by Firestore Security Rules. Trusted server libraries use Google Cloud IAM and bypass Firestore Security Rules. A server endpoint using an Admin SDK must perform its own user and record authorization checks.

Start with a clear owner model. For example, a task can contain an **ownerId** field that matches the signed-in Firebase Authentication UID. Later chapters show how to enforce this condition in Rules and in query structure.

## First project exercise

Use a development Firebase project and fictional data. Create a Firestore database, then add a collection named **projects** and a document with a stable ID such as **project-demo**.

~~~json
{
  "name": "Notes workspace",
  "ownerId": "demo-user",
  "createdAt": "server timestamp",
  "archived": false
}
~~~

The value shown for **createdAt** is a description, not a Firestore string value. In a real client write, use **serverTimestamp()** so Firestore supplies the time. The next chapters introduce SDK setup and supported value types.

Then write down the path to the document, which environment owns the project, which user should be able to read it, and which test will prove that another user is denied. This turns database setup into an explicit data and access decision.

## Check before continuing

- Can you explain the difference between a Firebase project, a database, a collection, and a document?
- Can you identify the path to a nested document?
- Have you chosen the development project and database location intentionally?
- Are the first Security Rules restrictive?
- Do you know whether client requests use Security Rules or server requests use IAM?

## Practice questions

1. How is a document different from a collection?
2. Why is Firestore called a NoSQL document database?
3. What path identifies the task in the example hierarchy?
4. Does registering another web app create a separate database?
5. Why should development and production data use separate projects?
6. What does a Firestore web configuration protect?
7. Which authorization system applies to mobile and web client SDK requests?
8. What should be checked before choosing a production database location?

## Main references

- [Cloud Firestore overview](https://firebase.google.com/docs/firestore)
- [Firestore data model](https://firebase.google.com/docs/firestore/data-model)
- [Firestore locations](https://firebase.google.com/docs/firestore/locations)
- [Get started with Firestore Security Rules](https://firebase.google.com/docs/firestore/security/get-started)
- [Use Firestore with a server client library](https://firebase.google.com/docs/firestore/security/rules-conditions#authentication)
