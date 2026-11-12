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
- optional provider-supported preview database names derived from PR number
- use an existing server unless the selected provider supports managed server lifecycle

Preview behavior is provider-dependent. Check the selected provider's capability
manifest and documentation before enabling preview databases, managed servers,
or automated cleanup. A profile name and PR-specific domain do not by themselves
create isolated infrastructure or guarantee that resources will be removed.

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

This shows profile naming only. Replace `hosting` with a configured provider
key. Add database or server lifecycle settings only when that provider supports
the requested operation. See [provider capabilities](./PROVIDERS.md),
[database lifecycle](./DATABASES.md), and [server lifecycle](./SERVER_LIFECYCLE.md).

## Recommended Workflow

1. validate configuration
2. apply the `preview` profile on PR open or update
3. run the configured destroy or orphan-cleanup flow on PR close, after checking
   that the provider supports cleanup for the resources involved

## Provider Differences

Preview support depends on provider capabilities for sites, databases, and
optionally servers. Cleanup can have narrower support than creation: for
example, a provider may enumerate and remove owned preview sites without being
able to identify preview databases safely. Review each provider's capability
manifest and ownership rules before relying on automatic cleanup.

## Failure Modes

- previews colliding on names: include PR-specific variables in domains and databases
- cleanup leaving resources behind: verify destroy flows and ownership expectations
- preview workflow missing PR variables: ensure GitHub Actions exports the required context values
