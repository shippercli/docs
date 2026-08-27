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

## Failure Modes

- stable and preview environments sharing one database name: make preview naming unique
- assuming a provider supports automated database lifecycle: confirm feature support first
- placing databases at the wrong config level: keep them under the project, using profile variables for environment-specific names
