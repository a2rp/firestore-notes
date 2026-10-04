# 2. JavaScript SDK and project configuration

[Back to notes index](../README.md)

| Previous | Notes index | Next |
| --- | --- | --- |
| [Previous: Firestore fundamentals and database setup](./01-firestore-fundamentals-and-database-setup.md) | [Notes index](../README.md) | [Next: Documents, collections, fields, and paths](./03-documents-collections-fields-and-paths.md) |

## Add the Firebase JavaScript SDK

For a JavaScript web application, install the Firebase package from npm. Commit the package manifest and lock file so local development and automated builds resolve the same dependency tree.

~~~bash
npm install firebase
~~~

The modular SDK imports only the functions your files use. For Firestore, the app package creates the Firebase app instance and the Firestore package gives you functions such as collection, doc, getDoc, setDoc, query, and where.

## Register the web app

In the Firebase console, open the intended development project, add a web app, and copy its configuration. The app registration connects the client SDK to the project. It does not create a separate database or grant access to records.

Keep the configuration in one module. Feature files should import the shared database instance instead of calling initializeApp repeatedly.

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

This example uses Vite environment variables. A different build tool has a different way to provide public configuration. Keep names and access patterns consistent with that tool.

## Select an environment

Use separate Firebase projects for development, preview, and production when the data or access should be isolated. Provide each build with the configuration for exactly one environment. Do not switch a production app to a development database at runtime based on a query parameter or user-controlled setting.

Example development environment file:

~~~text
VITE_FIREBASE_API_KEY=public-client-key
VITE_FIREBASE_AUTH_DOMAIN=sample-dev.firebaseapp.com
VITE_FIREBASE_PROJECT_ID=sample-dev
VITE_FIREBASE_STORAGE_BUCKET=sample-dev.appspot.com
VITE_FIREBASE_MESSAGING_SENDER_ID=1234567890
VITE_FIREBASE_APP_ID=1:1234567890:web:abcdef123456
~~~

Use the environment file format expected by the build tool. Keep local-only files out of source control when they contain developer-specific values, and provide a safe example file with placeholder values for other contributors.

Before writing data or deploying, verify the project ID in the build configuration. Project aliases in the Firebase CLI are useful, but the app's client configuration and the CLI target are separate choices. Check both.

## Keep public config separate from secrets

Values included in a browser bundle are visible to its users. Firebase web configuration is public client configuration and tells the SDK where to connect. It is not an access secret.

Never place service account private keys, Admin SDK credentials, private API keys, or privileged service credentials in a Vite variable that begins with VITE_. Those values must stay on a trusted server or in a protected deployment secret store.

Security comes from Security Rules, correct user authentication, server authorization, and careful IAM permissions. Hiding a project ID or a configuration object is not a replacement for these controls.

## Avoid duplicate app initialization

A normal browser application should initialize Firebase once from its shared module. If a development setup deliberately evaluates that module more than once, check for an existing app before initializing another one.

~~~js
import { getApp, getApps, initializeApp } from "firebase/app";

const app =
  getApps().length > 0
    ? getApp()
    : initializeApp(firebaseConfig);
~~~

Do not use a broad initialization guard to hide conflicting configurations. If the application can connect to more than one project, name each app and select it explicitly. Most single-project client apps need only one default app.

The server Admin SDK is a separate trusted environment. Do not reuse browser initialization code for privileged server work. A server process should initialize credentials through its supported identity and deployment configuration.

## Verify the connection

After initialization, confirm that the selected project ID is the intended development project. Start with a harmless read or write using fictional data and check the Firebase console for that same project.

If a request fails, check the browser console and network error, the app configuration, database creation, Security Rules, and whether the SDK is connected to the emulator. Do not loosen production Rules just to make a local request pass.

## Practice questions

1. Which npm package provides the Firebase JavaScript SDK?
2. Where should the Firebase app and Firestore instance be initialized?
3. Why should feature files reuse the shared Firestore instance?
4. How should a build choose between development and production projects?
5. Why are Vite variables that begin with VITE_ public?
6. Name two credentials that must not enter the browser bundle.
7. What should be verified before the client writes its first record?
8. When is an existing-app guard useful?

## Main references

- [Add Firebase to a JavaScript project](https://firebase.google.com/docs/web/setup)
- [Initialize Firebase in JavaScript](https://firebase.google.com/docs/web/setup#add-sdks-initialize)
- [Firebase JavaScript SDK reference](https://firebase.google.com/docs/reference/js)
- [Configure multiple projects](https://firebase.google.com/docs/projects/multiprojects)
- [Use environment configuration](https://firebase.google.com/docs/projects/learn-more#config-files-objects)
