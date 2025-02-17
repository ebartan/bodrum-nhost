---
title: 'Permissions'
sidebar_position: 1
---

The GraphQL API is protected by a role-based permission system based on access tokens. Permissions are handled on a per-table basis in Hasura Console.

---

## How it works

Upon login a user gets an access token which is then being sent with every GraphQL API request to authenticate the user and apply the correct permissions when reading and writing data.

In Hasura you can specify what a user is allowed to do. A common use case is to allow a user to read something if `user_id` in the database is equal to the user ID in the access token.

Access token data is included as headers with every API request. By default, every user has the following session variables that can be used when creating permission rules:

- `x-hasura-user-id`
- `x-hasura-allowed-roles`
- `x-hasura-default-role`

The default role for users is `user`.

> You can also [add custom permission](#add-permission-variables) varaibles if you need to.

---

## Select permissions

![Select permissions](/img/platform/permission-select.png)

One of the most common permission requirements is that logged-in users should only be able to read their own data. This is how it can be achieved with Hasura.

1. Go to **Hasura Console**
1. Select your table and open the **Permissions** tab
1. At the top of the page, click **"select"** on the **"user"** role.
1. Select **"With custom check"** to create a new rule
1. Enter `user_id`, `_eq` and `x-hasura-user-id` into the rule form.

This means that in order for users to read data, the user ID value in the database row must be the same as the user ID in the access token.

To further refine this rule, do the following:

1. Limit number of rows to 100 (or some other relevant number).
1. Select the columns you want the user to be able to read. In our case we'll allow the user to read all columns.

Note that if you add columns to your table table later, you must check new columns here to let users read them.

---

## Insert permissions

![Insert permissions](/img/platform/permission-insert.png)

Here is a popular approach for insert permission for logged in users.

1. At the top of the page, click **"insert"** on the **"user"** role.
1. Select **"Without any checks"**.
1. Select the columns you want to allow users to insert.

In our example, we only select `name`, because we want all other other columns to be filled with default values.

We also want every new record's `user_id` value to be set to the ID of the user making the request. We can tell Hasura to do this with **column presets**.

1. Under column presets, set `user_id` to `x-hasura-user-id`.

## Add Permission Variables

You can add extra permission variables in the Nhost console under **Users** and then **Roles and permissions**. These permission variables are then available when creating permissions for your GraphQL API in the Hasura console.

![Permission Variables](/img/platform/permission-variables-preview.svg)

As an example, let's say you add a new permission variable `x-hasura-organisation-id` with path `user.profile.organisation.id`. This means that Nhost Auth will get the value for `x-hasura-organisation-id` by internally generating the following GraphQL query:

```graphql
query {
  user(id: "<user-id>") {
    profile {
      organisation {
        id
      }
    }
  }
}
```
