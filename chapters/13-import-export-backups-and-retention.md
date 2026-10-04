# 13. Import, export, backups, and retention

[Back to notes index](../README.md)

| Previous | Notes index | Next |
| --- | --- | --- |
| [Previous: Aggregation and summary data](./12-aggregation-and-summary-data.md) | [Notes index](../README.md) | [Next: Emulator, Rules testing, and debugging](./14-emulator-rules-testing-and-debugging.md) |

## Know which protection you need

An export copies database data to Cloud Storage for movement, analysis, or recovery. A scheduled backup is a consistent point-in-time copy that can be restored to a database. Point-in-time recovery can recover data from a configured recent time window.

These options solve different problems. A backup helps recover from accidental changes or deletion. An export can move selected collection groups or feed another workflow. Neither removes the need to test restoration.

Check current product support, database edition, billing, location, IAM permissions, and retention requirements before enabling a feature. Keep exports and backups protected because they contain application data.

## Export data with the managed service

Managed Firestore export and import uses Cloud Storage and the Google Cloud CLI. For example, after configuring gcloud for the intended source project:

~~~bash
gcloud config set project sample-dev
gcloud firestore export gs://sample-firestore-backups/manual-check --database="(default)" --async
~~~

The command starts a long-running operation. Check its status with the Firestore operations commands and confirm the output path before using it. Exporting reads database documents and can incur usage charges.

An export is not guaranteed to be a snapshot taken at the exact start time. Writes that happen while the export runs may be represented in the exported data. If exact consistency matters, coordinate the application write process and follow the documented export procedure.

For a smaller transfer, managed export can select collection groups. A collection group export does not automatically include every nested subcollection beneath those collections. Plan exactly which collections and indexes the destination needs.

## Restore and move with care

Before importing, confirm the active project, database ID, export prefix, destination permissions, and expected effect on existing documents. Do not use a production project as a restore test.

Restore into an isolated database or project first. Verify document counts, sample records, indexes, application queries, and Security Rules. Record who approved the restore and how the live service will be switched back if validation fails.

Moving data between projects requires the destination to read the export files and have its required indexes configured. Keep the source intact until the destination is verified and the application has been safely moved.

## Set up backups and recovery

Scheduled backups can take daily or weekly database copies with a chosen retention period. A backup contains the database data and index configuration at a point in time. Review the current maximum retention and supported restore options for the database before setting a policy.

Point-in-time recovery is useful when the desired recovery point is more recent than a scheduled backup. Its recovery window and cost depend on current product configuration. Decide who can perform a restore and document the steps before an incident.

A backup is useful only if it can be restored. Run a restore exercise in a separate database, verify the result, and record the time required. Do not overwrite the live database as the first test.

## Use TTL for eventual expiration

A TTL policy can delete documents after a timestamp field reaches its expiration time. It is useful for temporary records such as short-lived sessions or old activity data.

TTL deletion is asynchronous, not an exact deadline. Expired documents can remain visible until the deletion process removes them, typically within 24 hours. TTL deletes the document but does not delete subcollections. TTL deletions also count as document deletes.

Do not use TTL when a record must disappear at an exact moment, when nested records need coordinated removal, or when the application must run a business action at expiration. Use a trusted process for those requirements.

## Protect exported files

Treat Cloud Storage export files as copies of the original database. Limit bucket access to the service identities and operators who need it, apply an appropriate retention policy, and avoid public access.

Do not put exports in a developer's public repository or attach them to issue reports. A backup may contain user data and must follow the same privacy and access expectations as the live database.

## Practice questions

1. How does a backup differ from an export?
2. Which service stores managed export files?
3. Why can an export include writes made while it is running?
4. What should be checked before importing into a destination database?
5. Why should a restore be tested in an isolated database?
6. What is point-in-time recovery for?
7. Does TTL remove a document at the exact expiration second?
8. Does deleting a document through TTL remove its subcollections?

## Main references

- [Export and import data](https://firebase.google.com/docs/firestore/manage-data/export-import)
- [Disaster recovery planning](https://firebase.google.com/docs/firestore/disaster-recovery)
- [Back up and restore data](https://firebase.google.com/docs/firestore/backups)
- [Point-in-time recovery](https://firebase.google.com/docs/firestore/use-pitr)
- [Manage data retention with TTL policies](https://firebase.google.com/docs/firestore/ttl)
