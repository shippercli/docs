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

## Application connection variables

Ploi, Forge, and EasyPanel provider versions supporting
`database_environment_prefixes` deliver each newly created database's connection
details to the application environment. A single database defaults to `DB_HOST`,
`DB_DATABASE`, `DB_USERNAME`, and `DB_PASSWORD`.

For multiple databases, configure a unique prefix for every declared database
name in the provider block. The keys are the `name` values from the declarations,
not the labels such as `main` and `cache`:

```yaml
providers:
  ploi:
    api_key: "${PLOI_API_KEY}"
    server_id: "123"
    database_environment_prefixes:
      myapp_production: DB
      myapp_cache: CACHE_DB
```

The cache connection then supplies `CACHE_DB_HOST`, `CACHE_DB_DATABASE`,
`CACHE_DB_USERNAME`, and `CACHE_DB_PASSWORD`. Configure the application to read
those variables. Prefixes must be unique uppercase environment identifiers.
Missing or ambiguous mappings fail validation before provisioning. Match
template names exactly when database names contain interpolation placeholders.

Each newly created database receives an independently generated password.
Forge and EasyPanel can use an explicitly configured shared `database_password`;
keep that value in an environment variable or secret manager. Never copy
generated passwords into deployment logs or plans. Check the installed provider
version's README for its supported mapping options before applying this example.

## Re-applying existing databases

Re-apply preserves existing database passwords and application secrets; it does
not rotate passwords implicitly. Existing credentials must already be available
to the application. Providers cannot recover a password that was discarded by an
earlier deployment. Verify the matching connection variables or restore them
from the secret manager before reusing an existing database.

Ploi requires the database's returned site association to match the deployment
site. Its documented API accepts `site_id` on database creation but does not
provide an operation to attach an existing unlinked database. A mismatched or
missing association causes apply to fail rather than report a working connection.
Do not delete and recreate a production database to bypass this check.

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
