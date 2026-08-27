---
description: "How Shipper creates, updates, deploys, and destroys application sites across environments."
section: "Deployment Features"
order: 30
---

# Site Lifecycle

This page explains how Shipper manages sites over time.

## What Shipper Does

For a site deployment, Shipper can:

- find an existing site by the configured domain
- create a site when it does not exist
- apply relevant site settings supported by the provider
- deploy the configured branch or source
- remove the site during destroy flows

## Inputs

Site behavior typically depends on:

- [Projects](./PROJECTS.md)
- [Profiles](./PROFILES.md)
- provider capabilities

## Minimal Example

```yaml
projects:
  api:
    provider: hosting
    profiles:
      production:
        branch: main
        domain: "api.example.com"
```

## Provider Differences

Source, deploy mode, root paths, and site settings vary by provider:

- [Ploi](/providers/ploi)
- [Laravel Forge](/providers/forge)
- [cPanel](/providers/cpanel)
- [EasyPanel](/providers/easypanel)

## Failure Modes

- site exists but under unexpected provider state: check the provider page for lookup behavior and supported settings
- relying on Git-based deployment where the provider uses another source model: confirm the provider workflow first
- destroy removing more than expected: review [Plan](./PLAN.md) before destructive actions
