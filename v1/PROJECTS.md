---
description: "How projects are defined, how repositories and paths are configured, and which settings are shared across profiles."
section: "Configuration"
order: 22
---

# Projects

Each project is one deployable application.

## Shape

```yaml
projects:
  api:
    provider: hosting
    path: ./apps/api
    profiles:
      production:
        branch: main
        domain: "api.example.com"
```

## Common Project Fields

### `provider`

The provider key this project uses.

### `path`

The local source path for this application inside the repository.

### `repository`

Repository metadata for providers that deploy from a remote repository.

This is provider-dependent. Do not assume every provider requires Git-based deployment.

### `web_directory`

The public web root when the provider supports it.

### `project_root`

The application root on the target when the provider supports it.

## Multiple Projects

```yaml
projects:
  api:
    provider: hosting
    path: ./apps/api
    profiles:
      production:
        branch: main
        domain: "api.example.com"

  web:
    provider: hosting
    path: ./apps/web
    profiles:
      production:
        branch: main
        domain: "app.example.com"
```

## Provider Differences

Repository, root, and deploy-source behavior vary by provider. Confirm on:

- [Ploi](/providers/ploi)
- [Laravel Forge](/providers/forge)
- [cPanel](/providers/cpanel)
- [EasyPanel](/providers/easypanel)

## Failure Modes

- treating project defaults as profile settings: keep environment-specific values under profiles
- assuming remote Git deployment is universal: verify provider behavior first
- reusing one project for multiple apps: split them into separate project entries
