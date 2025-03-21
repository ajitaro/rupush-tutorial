# Rupush CodePush Documentation

## Overview

Rupush is a tool for managing CodePush updates. This document provides instructions for setting up and using Rupush to deploy updates efficiently.

---

## Getting Started

### All Developers

All developers must register and log in before using Rupush for CodePush deployments.

#### Register with Your Office Email

```sh
fastlane register
```

Verify your email.

#### Login with Registered Email

```sh
fastlane login
```

#### Request to Join Organization
After logging in, request access to your organization by contacting your team leader. Your leader will add you as a collaborator to the appropriate organization.

#### Add Your CodePush Deployment

**Note: Naming Conventions**

- Use your name, and if you need multiple CodePush deployments, append `v2`, `v3`, etc.

Example:

```sh
aji
ajiv2
ajiv3
```

```sh
fastlane codepush_add
```

#### Deploy Your CodePush

```sh
fastlane codepush
```

---

## Superuser Setup

Superusers are responsible for setting up the organization, applications, deployments, and managing collaborators.

### 1. Create an Organization

Each organization should have distinct staging and production environments.

```sh
npx rupush organization add <name>
npx rupush organization add ruparupa-staging
npx rupush organization add ruparupa-production
```

### 2. Create Applications (Android & iOS)

Define the applications within the organization for different platforms.

```sh
npx rupush app add <organization> <name> <platform>
npx rupush app add ruparupa-staging app-android android
npx rupush app add ruparupa-staging app-ios ios
```

### 3. Create Deployments

#### TestingProduction Deployment

```sh
npx rupush deployment add <organization> <app> <name>
npx rupush deployment add ruparupa-staging app-android TestingProduction
npx rupush deployment add ruparupa-staging app-ios TestingProduction
```

#### Prepare-Deployment

```sh
npx rupush deployment add ruparupa-staging app-android prepare-deployment
npx rupush deployment add ruparupa-staging app-ios prepare-deployment
```

### 4. Invite Collaborators

Invite other developers to collaborate within an organization.

```sh
npx rupush collaborator add <organization> <email>
npx rupush collaborator add ruparupa-staging fitriaji.robbaanii@ruparupa.com
```

### 5. Optional: Rename Deployment

Change the deployment name if needed.

```sh
npx rupush deployment rename ruparupa-staging app-android Staging Smiley
npx rupush deployment rename ruparupa-staging app-ios Staging Smiley
```

### 6. Check Deployments

List all deployments for a given app in an organization.

```sh
npx rupush deployment ls <organization> <app>
npx rupush deployment ls ruparupa-staging app-android
npx rupush deployment ls ruparupa-staging app-ios
```

---

## Releasing and Promoting CodePush Updates

### 1. Release a CodePush Update

Release an update for a specific deployment and platform.

```sh
npx rupush release-react <organization> <app> <platform> --deploymentName <deploymentName>
npx rupush release-react ruparupa-staging app-android android --deploymentName Staging
npx rupush release-react ruparupa-staging app-ios ios --deploymentName Staging
```

### 2. Promote a CodePush Update

Promote an update from one deployment to another.

```sh
npx rupush promote <organization> <app> <from> <to>
npx rupush promote ruparupa-staging app-android Staging Production
npx rupush promote ruparupa-staging app-ios TestingProduction Production
```

---

## Summary

This document outlines the steps required to set up and manage Rupush for CodePush updates. By following these instructions, developers can efficiently deploy and manage updates.
