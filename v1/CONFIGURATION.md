---
description: "Entry point for structuring shipper.yml, with separate reference pages for concepts, providers, projects, profiles, commands, and deployment features."
section: "Configuration"
order: 20
---

# Configuration Reference

This page is the entry point for `shipper.yml`.

## Read Configuration from the Outside In

1. understand the [Concepts](./CONCEPTS.md)
2. define [Providers](./PROVIDERS.md)
3. define [Projects](./PROJECTS.md)
4. define [Profiles](./PROFILES.md)
5. configure feature-specific lifecycle pages as needed

## Minimal Shape

```yaml
providers:
  <provider_name>:
    # provider-specific settings

projects:
  <project_name>:
    provider: <provider_name>
    profiles:
      <profile_name>:
        branch: <branch>
        domain: <domain>
```

## Configuration by Topic

- [Providers](./PROVIDERS.md)
- [Projects](./PROJECTS.md)
- [Profiles](./PROFILES.md)
- [Site Lifecycle](./SITES.md)
- [Database Lifecycle](./DATABASES.md)
- [Server Lifecycle](./SERVER_LIFECYCLE.md)
- [PR Preview Environments](./PR_PREVIEWS.md)

## Provider Capability Differences

General structure is documented here. Exact fields and feature support vary by provider:

- [Ploi](/providers/ploi)
- [Laravel Forge](/providers/forge)
- [cPanel](/providers/cpanel)
- [EasyPanel](/providers/easypanel)

## Failure Modes

- mixed provider and project naming: keep the provider key stable and reference it exactly from projects
- environment-specific settings placed at project level: move them into profiles
- provider-specific fields copied into shared docs without checking support: confirm against the provider page
