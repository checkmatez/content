---
alias: aechi6iequ
path: /docs/reference/console/projects
layout: REFERENCE
description: In the Graphcool console, you can manage multiple GraphQL projects, define your GraphQL schema and create or modify your data set.
tags:
  - console
related:
  further:
  more:
---

# Project Settings

## General information

Here you can configure and view general settings for your project.

* The **project name** helps you to manage your different projects, but has no other effect.
* The **project id** is automatically generated and not customizable. It can be found in your API [endpoints]() as well.
* If you want to customize your API endpoints, you can set a custom **project alias**. Now you can use either the project id or project alias when using the project API or working wiht the [CLI]().
* You can also **clone your project** and decide to optionally include data or other project settings.

In the **danger zone** you can delete your projects data, schema, or delete your project all together. **These actions are not reversible**.

## Other settings

In the **authentication** tab, you can create and manage [permanent access tokens](). These are mainly used in serverless functions or other server-side code that needs full access to your API.

The **export** tab allows you to export your data as JSON files and download your schema. You can also use [get-graphql-schema](https://github.com/graphcool/get-graphql-schema) to download your schema for advanced uses.

Monthly usage is collected in the **billing** tab. This gives you insight into your monthly requests and data storage and allows you to upgrade your plan.

> Currently, only the owner of a project has access to the billing tab. Please reach out to us if you need to transfer ownership of one of your projects!

The **team** tab allows you to view your collaborators and manage seats if you're the owner of the project.
