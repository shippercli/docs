---
description: "How databases are declared, named, created, linked, and cleaned up across deployment profiles."
section: "Deployment Features"
order: 40
---

# Database Lifecycle

This page explains how Shipper manages databases by environment.

## Shape

```yaml
projects:
  api:
    databases:
      main:
        name: "myapp_production"
        user: "myapp_production"
        type: mysql
    profiles:
      production:
```

## Fields

### `name`

Database name. Supports variable interpolation.

### `user`

Database username. Supports variable interpolation.

### `type`

Database engine.

## Multiple Databases

```yaml
projects:
  api:
    databases:
      main:
        name: "myapp_production"
        user: "myapp_production"
        type: mysql
      cache:
        name: "myapp_cache"
        user: "myapp_cache"
        type: mysql
    profiles:
      production:
```

## Preview Pattern

```yaml
name: "myapp_${PROJECT_NAME}_${PROFILE}_${GITHUB_PR_NUMBER}"
```

## Provider Differences

Database creation and linking behavior vary by provider. Confirm support on the provider page.

[Ploi 2.0.0](https://github.com/shippercli/provider-ploi/tree/v2.0.0), [Forge 1.0.0](https://github.com/shippercli/provider-forge/tree/v1.0.0), and [EasyPanel 2.1.0](https://github.com/shippercli/provider-easypanel/tree/v2.1.0) expose a single new database to the application as `DB_HOST`, `DB_DATABASE`, `DB_USERNAME`, and `DB_PASSWORD` by default. For multiple databases, set `database_environment_prefixes` under the provider and map each declared database name to a distinct uppercase prefix. For example, the `myapp_cache` database declared above can use the prefix `CACHE_DB` to expose `CACHE_DB_HOST`, `CACHE_DB_DATABASE`, `CACHE_DB_USERNAME`, and `CACHE_DB_PASSWORD`.

Reapplying with these providers preserves existing database passwords. Provide the existing connection settings in the application environment or its configured overrides. Ploi requires the database to already be linked to the site. Forge preserves databases on destroy. EasyPanel requires a matching lifecycle marker to reuse an existing database service and preserves unmarked services during cleanup. Check the linked release documentation for each provider's full reuse requirements.

[Coolify 1.0.0](https://github.com/shippercli/provider-coolify/tree/v1.0.0#existing-database-declaration-credentials) uses `SHIPPER_DB_<NORMALIZED_NAME>_*` for per-database connection settings and exposes the primary SQL or SQLite connection as `DB_*`. If Coolify redacts an existing database's connection settings, supply them explicitly in the application environment.

Use the [provider verification matrix](https://github.com/shippercli/provider-gap-matrix#distribution-and-verification) to check current live verification coverage.

## Failure Modes

- stable and preview environments sharing one database name: make preview naming unique
- assuming a provider supports automated database lifecycle: confirm feature support first
- placing databases at the wrong config level: keep them under the project, using profile variables for environment-specific names
