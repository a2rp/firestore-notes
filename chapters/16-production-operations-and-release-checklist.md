# 16. Production operations and release checklist

[Back to notes index](../README.md)

| Previous | Notes index | Next |
| --- | --- | --- |
| [Previous: Performance, cost, and scalability](./15-performance-cost-and-scalability.md) | [Notes index](../README.md) | [Next: All code samples](./98-all-code-samples.md) |

## Keep environments and permissions separate

Use separate Firebase projects for development, staging, and production when they need isolated data or different access. Make the active target obvious in the CLI and console. Check the project ID before any production command.

Give production access only to people and service identities that need it. Use least-privilege IAM roles, keep server credentials out of client builds, and require review for changes to database Rules, indexes, and deployment configuration.

## Keep database configuration in source control

Track firestore.rules and firestore.indexes.json with the app code. Point firebase.json at those files so local tests and deployments use the reviewed versions.

~~~json
{
  "firestore": {
    "rules": "firestore.rules",
    "indexes": "firestore.indexes.json"
  }
}
~~~

The Firebase CLI can deploy Rules and indexes separately:

~~~bash
firebase deploy --only firestore:rules
firebase deploy --only firestore:indexes
~~~

Deploying Rules from the CLI replaces the Rules currently published for the configured database. If someone edited Rules in the console, bring that change into the source-controlled file before the next deployment. Review the destination project and the exact diff before deploying.

## Review schema changes and migrations

Firestore does not require one fixed schema, but application versions still depend on field names, types, and query indexes. When changing a field shape, plan for old and new app versions to coexist during the release.

A safe migration can add a new field first, update reads to accept both shapes, backfill existing documents, then write only the new shape. Remove the old field after clients and jobs no longer need it. Make migration jobs resumable and safe to run again.

Build required indexes before releasing code that depends on them. Wait for index creation to finish and test the exact query against a non-production project.

## Use a staged release process

1. Review the change and confirm the target Firebase project.
2. Run application checks and Firestore Rules tests against the emulator.
3. Review query changes, required indexes, and expected read and write volume.
4. Deploy indexes and wait until they are ready.
5. Deploy the tested Rules and application changes in a compatible order.
6. Verify a signed-in user can complete the key reads and writes.
7. Watch errors, latency, and usage after the release.
8. Keep a recovery plan for application code and database data.

Use preview and staging projects to catch configuration and access differences before production. Avoid deploying every Firebase service when only Firestore Rules or indexes changed.

## Plan rollback and recovery

A code rollback changes application behavior but does not reverse database writes already accepted. A Rules rollback changes access policy but cannot restore records deleted by a client or server.

Know which earlier application versions remain compatible with the current data shape. For destructive data changes, take a suitable backup and test restoration in an isolated database. Record how to stop a migration, restore data, and redeploy a compatible version.

A release is not complete when the command exits successfully. Confirm the expected project, deployed configuration, critical user flow, error rate, and database usage.

## Production checklist

Before release:

- Confirm the Firebase project ID and database ID.
- Review Rules for unintended broad access.
- Test owner, other-user, and signed-out cases.
- Confirm required composite indexes are ready.
- Check migration behavior against existing records.
- Confirm service credentials and secrets are protected.
- Review expected reads, writes, deletes, and data transfer.
- Identify the recovery path for application and data changes.

After release:

- Verify the main read and write flows with a real test account.
- Check permission errors, latency, and listener behavior.
- Review Firestore usage and billing dashboards.
- Confirm the app is using the intended Rules and indexes.
- Record the source commit and release time.

## Practice questions

1. Why should production use a separate Firebase project?
2. Where should Firestore Rules and indexes be stored?
3. What happens to console Rules when the CLI deploys the local Rules file?
4. Why should indexes be ready before code depends on them?
5. How can a field migration support old and new app versions?
6. Does rolling back application code reverse database writes?
7. Which access cases should be tested before release?
8. What should be checked after a successful deployment?

## Main references

- [Firebase CLI deploy reference](https://firebase.google.com/docs/cli#deployment)
- [Manage and deploy Security Rules](https://firebase.google.com/docs/rules/manage-deploy)
- [Index configuration reference](https://firebase.google.com/docs/reference/firestore/indexes)
- [Firestore disaster recovery planning](https://firebase.google.com/docs/firestore/disaster-recovery)
- [Firestore best practices](https://firebase.google.com/docs/firestore/best-practices)
