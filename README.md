# Cloud Firestore Study Notes

These are my personal study notes from learning and working with Cloud Firestore. They collect the core concepts, JavaScript examples, security decisions, and practical checks I want to keep in one dependable reference.

The notes use the Firebase modular JavaScript SDK and focus on Firestore for web applications. They begin with documents and collections, then build through writes, queries, data modeling, Security Rules, offline behavior, testing, and production operations.

## About these notes

This repository records what I study and practice while working with Firestore. Each chapter explains a core idea in plain language, then connects it to practical JavaScript examples and common decisions made in an application.

The examples use fictional records and project names. Firebase web configuration identifies a project but does not protect data. Client access must be controlled by Security Rules, and privileged server work must be protected with server-side authorization and IAM.

## Core topics

- Firestore projects, databases, locations, and the JavaScript SDK
- Collections, documents, paths, field types, and references
- Creating, updating, deleting, and batching writes
- Reading documents, filtering, ordering, and query limits
- Indexes, cursors, pagination, and real-time listeners
- Transactions, atomic batches, and concurrent changes
- Security Rules, authentication, and field validation
- Data modeling, subcollections, denormalization, and cleanup
- Offline cache, synchronization, aggregation, and summaries
- Emulator testing, performance, cost, operations, and release checks

## Chapters

01. [Firestore fundamentals and database setup](./chapters/01-firestore-fundamentals-and-database-setup.md)
   Understand what Firestore is, how databases and projects relate, and what to decide before creating a database.

02. [JavaScript SDK and project configuration](./chapters/02-javascript-sdk-and-project-configuration.md)
   Install the modular SDK, initialize Firestore once, and select the right Firebase project for each environment.

03. [Documents, collections, fields, and paths](./chapters/03-documents-collections-fields-and-paths.md)
   Read Firestore paths and learn how documents, collections, fields, and subcollections fit together.

04. [Data types, field values, and references](./chapters/04-data-types-field-values-and-references.md)
   Choose suitable field types for dates, arrays, maps, references, and server-generated values.

05. [Create, update, delete, and write patterns](./chapters/05-create-update-delete-and-write-patterns.md)
   Add and change data with document references, merge options, field transforms, and safe delete flows.

06. [Reading data and building queries](./chapters/06-reading-data-and-building-queries.md)
   Fetch one document or a query result and build clear filters, ordering, and bounded reads.

07. [Query indexes, pagination, and listeners](./chapters/07-query-indexes-pagination-and-listeners.md)
   Understand index requirements, cursor pagination, live snapshots, and listener cleanup.

08. [Transactions, batches, and concurrency](./chapters/08-transactions-batches-and-concurrency.md)
   Choose between a transaction and a batch, handle concurrent updates, and keep atomic work reliable.

09. [Security Rules, authentication, and validation](./chapters/09-security-rules-authentication-and-validation.md)
   Restrict client reads and writes with ownership checks, field validation, and emulator tests.

10. [Data modeling, relationships, and cleanup](./chapters/10-data-modeling-relationships-and-cleanup.md)
   Model related records with subcollections or references and plan duplication and deletion behavior.

11. [Offline data, cache, and conflicts](./chapters/11-offline-data-cache-and-conflicts.md)
   Understand local reads, cached queries, pending writes, synchronization, and web persistence choices.

12. [Aggregation and summary data](./chapters/12-aggregation-and-summary-data.md)
   Count and summarize matching records while balancing current values, query cost, and maintained counters.

13. [Import, export, backups, and retention](./chapters/13-import-export-backups-and-retention.md)
   Plan data movement, managed backups, retention, and safe restore checks.

14. [Emulator, Rules testing, and debugging](./chapters/14-emulator-rules-testing-and-debugging.md)
   Run the Firestore emulator, test allowed and denied access, and investigate rule and query failures.

15. [Performance, cost, and scalability](./chapters/15-performance-cost-and-scalability.md)
   Reduce unnecessary reads, design for indexes and contention, and monitor usage as data grows.

16. [Production operations and release checklist](./chapters/16-production-operations-and-release-checklist.md)
   Review environments, access, monitoring, recovery, and the checks that protect a production release.

## Reference chapters

- [All code samples](./chapters/98-all-code-samples.md) collects JavaScript and Rules examples from the core chapters.
- [Complete questions and answers](./chapters/99-complete-q-and-a.md) gathers review questions from all core chapters with clear answers.

## How to use these notes

Read the chapters in order when learning Firestore, or open the section that answers a question from your current project. Try examples with fictional data and use the Firestore emulator to check Security Rules before applying them to a live database.

## Main references

- [Cloud Firestore documentation](https://firebase.google.com/docs/firestore)
- [Firestore data model](https://firebase.google.com/docs/firestore/data-model)
- [Add and update data](https://firebase.google.com/docs/firestore/manage-data/add-data)
- [Read and query data](https://firebase.google.com/docs/firestore/query-data/queries)
- [Firestore Security Rules](https://firebase.google.com/docs/firestore/security/get-started)
- [Firestore offline data](https://firebase.google.com/docs/firestore/manage-data/enable-offline)
- [Firestore pricing](https://firebase.google.com/docs/firestore/pricing)

## Links

- Portfolio: https://www.ashishranjan.net
- GitHub: https://github.com/a2rp
- CodePen: https://codepen.io/ash1198
- LinkedIn: https://www.linkedin.com/in/aashishranjan
- Facebook: https://www.facebook.com/theash.ashish/
- YouTube: https://www.youtube.com/@ashishranjan-ashz?sub_confirmation=1
- Email: mailto:ash.ranjan09@gmail.com

## Support

- Support: https://a2rp-donation-page.netlify.app/
- Buy Me a Coffee: https://buymeacoffee.com/ashishranjan
- Patreon: https://www.patreon.com/ashishranjan
