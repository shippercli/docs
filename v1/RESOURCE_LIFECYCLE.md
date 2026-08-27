---
description: "How Shipper thinks about creating, reusing, updating, and destroying provider-managed resources."
section: "Core Concepts"
order: 16
---

# Resource Lifecycle

Shipper manages resources by lifecycle, not just by static configuration.

## Lifecycle Stages

### Validate

Checks that the requested resource configuration is coherent.

### Plan

Shows whether resources are expected to be created, reused, updated, or destroyed.

### Apply

Executes the create or update work.

### Destroy

Removes resources that belong to the selected deployment flow.

## Common Resource Types

- sites
- databases
- environment-specific preview resources
- managed servers

## Ownership

Shipper must distinguish between:

- resources it may safely create and remove
- resources that already existed and should only be reused

This is especially important for temporary environments and managed server cleanup.

## Feature Pages

- [Site Lifecycle](./SITES.md)
- [Database Lifecycle](./DATABASES.md)
- [PR Preview Environments](./PR_PREVIEWS.md)
- [Server Lifecycle](./SERVER_LIFECYCLE.md)
