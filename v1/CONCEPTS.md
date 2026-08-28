---
description: "The core mental model behind Shipper: providers, projects, profiles, and resource lifecycle."
section: "Core Concepts"
order: 15
---

# Concepts

Shipper is easier to understand when the core concepts are kept separate.

## Provider

A provider is the deployment platform integration.

It answers questions like:

- how authentication works
- which resources can be managed
- which features are supported
- which provider-specific fields are required

Read: [Providers](./PROVIDERS.md)

## Project

A project is one deployable application.

It answers questions like:

- which app is being deployed
- which provider it uses
- where its code lives
- which defaults apply across environments

Read: [Projects](./PROJECTS.md)

## Profile

A profile is one environment for a project.

It answers questions like:

- which branch is deployed
- which domain is used
- which databases exist for that environment
- whether infrastructure is reused or provisioned

Read: [Profiles](./PROFILES.md)

## Resource Lifecycle

A resource lifecycle describes how Shipper treats provider-managed resources over time.

That includes:

- create vs reuse
- update behavior
- cleanup and destroy behavior
- ownership boundaries for temporary infrastructure

Read: [Resource Lifecycle](./RESOURCE_LIFECYCLE.md)

## Practical Rule

Think about the config in this order:

1. provider
2. project
3. profile
4. feature-specific lifecycle
