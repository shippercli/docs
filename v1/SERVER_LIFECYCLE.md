---
description: "How Shipper provisions, reuses, and cleans up servers as part of a deployment profile."
section: "Deployment Features"
order: 35
---

# Server Lifecycle

This page explains how Shipper manages server provisioning per profile.

## Overview

Server lifecycle support lets a profile:

- reuse an existing server
- create a managed server on demand
- destroy or retain managed servers after temporary environments are cleaned up

## Example

```yaml
projects:
  api:
    provider: hosting
    profiles:
      production:
        branch: main
        domain: "api.example.com"
        infrastructure:
          server:
            mode: existing
            server_id: 1337

      preview:
        branch: "${GITHUB_HEAD_REF}"
        domain: "api-preview-${GITHUB_PR_NUMBER}.example.com"
        infrastructure:
          server:
            mode: create
            name: "api-preview-${GITHUB_PR_NUMBER}"
            cleanup: destroy
```

## Modes

### `existing`

Reuse a known server.

### `create`

Provision a managed server for the environment.

## Cleanup Policies

### `destroy`

Remove a managed server when teardown is allowed and the server belongs to the deployment flow.

### `retain`

Keep the server after teardown.

### `manual`

Require explicit operator cleanup.

## Provider Differences

Server provisioning is not universal. Confirm support and exact fields on the provider page.

## Failure Modes

- assuming all providers can provision servers: verify feature support first
- using destroy cleanup for long-lived environments: prefer `existing` or `retain`
- missing ownership boundary for temporary infrastructure: review resource lifecycle expectations before cleanup
