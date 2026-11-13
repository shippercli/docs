---
description: "How preview environments are named, deployed, and cleaned up for pull requests."
section: "Deployment Features"
order: 50
---

# PR Preview Environments

This page explains how to run temporary pull request environments.

## Typical Pattern

- branch from PR context
- preview domain derived from PR number
- PR-specific database names where the provider supports preview databases
- an existing server or a disposable server where the provider supports server lifecycle

## Example

```yaml
projects:
  api:
    provider: hosting
    profiles:
      preview:
        branch: "${GITHUB_HEAD_REF}"
        domain: "api-preview-${GITHUB_PR_NUMBER}.example.com"
```

Replace `hosting` with a configured provider key and supply that provider's required deployment settings. This example shows how to name the preview profile and route. For separate infrastructure, check the [provider capabilities](./PROVIDERS.md), [database lifecycle](./DATABASES.md), and [server lifecycle](./SERVER_LIFECYCLE.md) before adding resources.

## Recommended Workflow

1. validate configuration
2. apply the `preview` profile on PR open or update
3. On PR close, use the original PR values with the provider's supported destroy or orphan cleanup operation.

## Provider Differences

Check which resources your provider can create and remove. A PR-specific hostname identifies the preview route. Database and server isolation require their own supported configuration. A provider that can remove an owned preview site may still require manual cleanup of its database. Verify cleanup against a disposable preview before enabling it in production.

## Failure Modes

- previews colliding on names: include PR-specific variables in domains and databases
- cleanup leaving resources behind: verify destroy flows and ownership expectations
- preview workflow missing PR variables: ensure GitHub Actions exports the required context values
