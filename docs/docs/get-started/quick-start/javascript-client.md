---
title: 'JavaScript client'
---

In the previous section, you used the Hasura Console to fetch a list of todos. Now, you will write a small JavaScript client to interact and retrieve todos from your Nhost app.

### Frontend frameworks

Nhost is framework-agnostic and works with any frontend you might build. You can also connect to Nhost from your server-side if you wish.

In this guide, we'll keep the example simple. We're not using a frontend framework. In a real-life scenario, you'd probably build a frontend client with a framework such as React, Vue, Svelte or React Native.

---

## Setup

> Make sure you have [Node.js](https://nodejs.org) and [npm](https://docs.npmjs.com/getting-started) installed.

Create a new folder called `nhost-todos`, and initialize a new JavaScript app there:

Using npm package manager

```bash
npm init -y
```

or

Using Yarn package manager

```bash
yarn init -y
```

> You might have to edit the `package.json` file and add/change the `type` object to `module` (`"type": "module"`).

Install Nhost JavaScript SDK:

Using npm package manager

```bash
npm install @nhost/nhost-js
```

or
Using Yarn package manager

```bash
yarn add @nhost/nhost-js
```

---

## Initialize Nhost

In the new directory, create a file called `index.js`.

Enter the following code into this file. It will initialize a new `NhostClient` that will interact with your backend:

```js
import { NhostClient } from '@nhost/nhost-js';

const nhost = new NhostClient({
  backendUrl: 'https://[app-subdomain].nhost.run', // replace this with the backend URL of your app
});

console.log(nhost.graphql.getUrl());
```

Run the code in your terminal. You should see your app's GraphQL endpoint URL:

```bash
➜ node index.js

https://[app-subdomain].nhost.run/v1/graphql
```

### Query todos

If you now add the following GraphQL query to the client, let's see what happens when you run the updated version:

```js
import { NhostClient } from '@nhost/nhost-js';

const nhost = new NhostClient({
  backendUrl: 'https://[app-subdomain].nhost.run',
})(async () => {
  // nhost.graphql.request returns a promise, so we use await here
  const todos = await nhost.graphql.request(`
    query {
      todos {
        id
        created_at
        name
        is_completed
      }
    }
  `);

  // Print todos to console
  console.log(JSON.stringify(todos.data, null, 2));
})();
```

```bash
➜ node index.js

null
```

`null` is printed. Why is that? Let's find out.
