---
source_url: "https://docs.cloud.google.com/identity-platform/docs/sign-in-user-email"
source_title: "Sign in a user with an email by using Identity Platform"
ingested_date: "2025-02-17"
topic: "authentication"
---

# Sign in a user with an email by using Identity Platform

**Google Cloud service:** [Identity Platform](https://cloud.google.com/identity-platform) — user authentication (email/password, social, OIDC, SAML, MFA, etc.).

## Summary

Identity Platform is used to sign in users with email and password. It supports Web SDK v9 (modular) and v8 (legacy), and integrates with Firebase Auth–compatible APIs.

## Prerequisites

- Google Cloud project with billing enabled.
- IAM roles: **Identity Platform Admin**, **Service Usage Admin**.

## Enable Identity Platform

1. Cloud Console → **Identity Platform** (Cloud Marketplace).
2. Click **Enable Identity Platform**.

## Configure email sign-in

1. Go to **Identity Providers**.
2. **Add a provider** → select **Email/Password**.
3. Enable and save.

## Create a user

1. **Users** page → **Add user**.
2. Enter email and password (needed for sign-in step).

## Sign in (SDK)

- **v9 (modular):** `npm install firebase`; initialize with `apiKey` and `authDomain`; use `signInWithEmailAndPassword(auth, email, password)` and `onAuthStateChanged`.
- **v8 (legacy):** Copy initialization from Identity Providers → Application setup details; use `firebase.auth().signInWithEmailAndPassword(email, password)`.

## Limits

- Unsuccessful sign-in attempts can temporarily lock the account.
- See [Quotas and limits](https://cloud.google.com/identity-platform/docs/quotas) for Identity Platform.

## Cleanup

- Delete provider and user from Identity Providers / Users, or delete the project.

## Reference

- **URL:** [Sign in a user with an email by using Identity Platform](https://docs.cloud.google.com/identity-platform/docs/sign-in-user-email)
- **Cited:** 2025-02-17
