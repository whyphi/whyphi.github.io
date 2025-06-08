---
title: Rush
type: docs
# prev: onboarding
# next: onboarding/zap/aws
weight: 3
sidebar:
  open: true
---

### Node.js & Prerequisites

To get started with Portal, ensure that you have Node.js [downloaded](https://nodejs.org/en/download) and your node version is `v20.x.x`.

Before you start working with this project, make sure you have the following prerequisites in place:

- Node.js: Ensure that you have Node.js installed. If not, you can download it from here. Verify your Node.js version by running the following command:

  ```
  node -v
  ```

- Your Node.js version should be at least v20.x.x. If not, consider updating Node.js using the following command:
  ```
  npm install -g n
  ```

### Getting Started

To install all necessary dependencies to run the project:

```
npm install
```

To start the development server, run the following command:

```
npm run dev
```

Open http://localhost:3000 with your browser to see the result.

### `.env.local`

Since Portal uses a lot of different services throughout different features and functionality, there are lot of sensitive information and data that needs to be used. For our application, we handle environment variables through a file called `.env.local`. Create this file on the root of the project.

Once you created `.env.local`, get the necessary credentials from another member in the team and copy and paste it. If the project is still not working with the credentials, restart your application.

`.env.local` should look something similar to this:

```{filename=".env.local"}
NEXT_PUBLIC_API_BASE_URL=http://xxxxxxxxxxxxxxxxx
GOOGLE_CLIENT_ID=xxxxxxxxxxxxxxxxx
GOOGLE_CLIENT_SECRET=xxxxxxxxxxxxxxxxx
MONGO_USER=xxxxxxxxxxxxxxxxx
MONGO_PASSWORD=xxxxxxxxxxxxxxxxx
NEXTAUTH_SECRET=xxxxxxxxxxxxxxxxx
NEXTAUTH_URL=http://xxxxxxxxxxxxxxxxx
...
```

### Directory Structure

WORK IN PROGRESS
