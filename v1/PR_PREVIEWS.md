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
- preview database names derived from PR number
- optional managed server creation with cleanup

## Example

```yaml
projects:
  api:
    provider: hosting
    databases:
      main:
        name: "myapp_preview_${GITHUB_PR_NUMBER}"
        user: "myapp_preview_${GITHUB_PR_NUMBER}"
        type: mysql
    profiles:
      preview:
        branch: "${GITHUB_HEAD_REF}"
        domain: "api-preview-${GITHUB_PR_NUMBER}.example.com"
        infrastructure:
          server:
            mode: create
            name: "api-preview-${GITHUB_PR_NUMBER}"
            cleanup: destroy
```

## Recommended Workflow

1. validate configuration
2. apply the `preview` profile on PR open or update
3. destroy the `preview` profile on PR close when cleanup is intended

## Provider Differences

Preview support depends on provider capabilities for sites, databases, and optionally servers.

## Failure Modes

- previews colliding on names: include PR-specific variables in domains and databases
- cleanup leaving resources behind: verify destroy flows and ownership expectations
- preview workflow missing PR variables: ensure GitHub Actions exports the required context values
