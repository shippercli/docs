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

The behavior in this section is verified in these **unmerged audit PRs**;
it is not a guarantee about the latest published packages:

| Provider | Audit PR | Verified source |
| --- | --- | --- |
| Ploi | [#26](https://github.com/shippercli/provider-ploi/pull/26) | `834ee5ac3edf722d1c63be9798857a25eeabee7c` |
| Forge | [#9](https://github.com/shippercli/provider-forge/pull/9) | `042f90a2199f52a4afbe8d129ddeadeeb483b849` |
| EasyPanel | [#14](https://github.com/shippercli/provider-easypanel/pull/14) | `5720cd2e754ae771377ccaf54cfc826064b84b62` |

Check the installed version's README and release notes before relying on these
options. These source checks do not establish live database connectivity.

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

Database declarations must target distinct resources after provider name
resolution. For example, Forge and Ploi reject `${PROJECT_NAME}_${PROFILE}`
alongside `api_production` when the project is `api` and profile is `production`.
Different environment prefixes cannot make those two declarations separate
databases. EasyPanel rejects database service names that collide after lowercase
normalization, punctuation replacement or truncation to 48 characters.

### Choosing creation passwords

For a new database, a nonempty application `<PREFIX>_PASSWORD` takes precedence
over automatic generation. The selected value is sent unchanged to database
creation and application delivery. Profile application values override project
values. Forge and EasyPanel use provider-wide `database_password` as a fallback;
otherwise passwords are generated independently. EasyPanel also considers
retained application environment values when recreating a missing service.

Empty explicit application passwords fail preflight. Raw application environment
content must be complete: an unfinished quoted value cannot be silently ignored.
Database names, users and hosts remain controlled by declarations/provider
settings; supplying a password does not redirect the connection. Existing
database credentials are not rotated by this creation behavior.

The following excerpt combines the Ploi mapping above with two explicit secrets:

```yaml
providers:
  ploi:
    api_key: "${PLOI_API_KEY}"
    server_id: "123"
    database_environment_prefixes:
      myapp_production: DB
      myapp_cache: CACHE_DB
projects:
  api:
    provider: ploi
    databases:
      main:
        name: myapp_production
        user: myapp_production
        type: mysql
      cache:
        name: myapp_cache
        user: myapp_cache
        type: mysql
    profiles:
      production:
        environment:
          variables:
            DB_PASSWORD: "${PRIMARY_DATABASE_PASSWORD}"
            CACHE_DB_PASSWORD: "${CACHE_DATABASE_PASSWORD}"
```

Export all three referenced variables before loading the configuration and add
your application source/domain configuration before deployment. The CLI resolves
exported references in these canonical environment variables. The audit also
includes [unmerged CLI #158](https://github.com/shippercli/cli/pull/158) at
`52551f26cb22b24786a13817591beb4c1ac430dc`, which prevents a second expansion of
imported secrets in profile provider configuration.
Never copy credentials into deployment logs or plans. This example is a
configuration/delivery check, not proof of database connectivity.

## Coolify audit connection format

The format below is implemented in the **unmerged**
[Coolify audit PR #1](https://github.com/shippercli/provider-coolify/pull/1),
verified against source `b467b4a6a683fa67137528596aa8bcc024cd704c`.
It is not a claim about a published provider release. Check the installed
provider's README before using these fields.

Coolify supplies namespaced variables derived from the normalized database
resource name. For declaration name `main`, the prefix is `SHIPPER_DB_MAIN_`;
for its PR 42 resource, it is `SHIPPER_DB_MAIN_PR_42_`. Duplicate declarations
and names that collide after normalization or preview truncation are rejected.

| Suffix | Meaning |
| --- | --- |
| `HOST`, `PORT` | Reported endpoint components, when available |
| `DATABASE`, `USERNAME`, `PASSWORD` | Available database and credential components |
| `URL` | Full reported connection URI, with known credential/database updates applied |

The primary SQL connection also uses `DB_CONNECTION`, `DB_HOST`, `DB_DATABASE`,
`DB_USERNAME` and `DB_PASSWORD`. Redis, Dragonfly and KeyDB require namespaced
`HOST` and `PASSWORD`; MongoDB and ClickHouse additionally require `USERNAME`.
The host must be nonempty. An explicitly supplied empty credential is accepted;
a missing credential is not inferred to mean passwordless authentication.
Unavailable required fields stop application environment operations.

Configure the application to consume the namespaced `URL` when URI options or
multiple hosts matter. It preserves topology and query options such as MongoDB
`authSource`; no framework-specific default URL variable is set automatically.
PostgreSQL named URI fields are reflected in component variables, and known
credential updates replace stale query credentials as well as userinfo.
Treat a URL containing credentials as a secret and keep it out of logs.

Explicit application environment values take precedence over generated values.
The CLI expands exported `${NAME}` references when loading the YAML. Export
referenced secrets before running Shipper; unset references remain unresolved.
For example, this connection-value excerpt supplies already-known Redis values
that must match the database's actual configuration:

```yaml
projects:
  api:
    provider: coolify
    databases:
      main:
        type: redis
    profiles:
      production:
        environment:
          variables:
            SHIPPER_DB_MAIN_HOST: "${REDIS_HOST}"
            SHIPPER_DB_MAIN_PASSWORD: "${REDIS_PASSWORD}"
```

Combine the excerpt with your provider and application source configuration.
PR previews use separately configured `coolify.preview_environment_variables`;
production project/profile secrets do not fill missing preview values.

These are field-delivery and request-scope checks. Live database connectivity,
application driver options, authentication/TLS and complete deployment/cleanup
flows still require verification. Earlier resource operations may already have
occurred when a later connection check fails; apply is not transactional.

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

## Partial provisioning and environment verification

In the audited Ploi and Forge versions above, each confirmed database creation
writes its connection variables to the site environment before another database
is created. This makes earlier successful connections available when a later
creation fails. A fresh-provider retry is covered by simulated API tests for both
providers; these tests do not prove live account behavior or database connectivity.

Ploi additionally reads each connection back before continuing and verifies the
selected values after its final environment update. Missing, malformed or
explicitly failed environment responses cause refusal. Configuration steps run
sequentially, so a failed step stops later configuration changes. Forge refuses
to merge into malformed or incomplete existing dotenv content and verifies the
requested values through a fresh read after every environment write. Ignored or
unreadable writes stop later provisioning steps.

At the EasyPanel audit commit above, each confirmed database creation also
writes the accumulated connection variables into the application environment
and reads them back before another database is created. A fresh-provider retry
reuses the first confirmed database and password after a later creation fails.
Missing, malformed, or mismatched readback stops further provisioning with a
fixed diagnostic.

EasyPanel verifies its final app environment update before configuring workers,
schedules, domains, or triggering deployment. Readback must contain every
expected variable, including retained settings, ownership markers, empty values,
numeric values, and literal secrets. Server-added unrelated variables are
allowed. Ignored writes, missing or wrong-type content, incomplete dotenv, and
lost retained variables cause refusal. Worker and scheduler environment writes
are also read back and compared before deploying workloads or restarting
schedulers. Missing, malformed, or mismatched readback stops those follow-on
operations.

These EasyPanel behaviors are covered by public-flow unit tests and ten real
local HTTP scenarios across database/app and worker/scheduler writes. The
worker/scheduler tests include successful, ignored, malformed, missing, wrong
type, and lost-value responses, plus checks that a failed readback blocks the
follow-on trigger. A negative control confirmed that the suite detects an
ignored worker write when verification is disabled. The production-only ZIP
consumer passes all ten HTTP suites, and PHP 8.3–8.5 CI passes at the source
revision above. These checks do not prove database connectivity or live
EasyPanel persistence behavior, and the audit PR is unmerged.

These operations are not atomic. Creation may succeed before credentials are
stored, and a failed or ambiguous response may leave a resource behind. None of these three
audited providers has a durable credential journal or automatic multi-database
rollback. Inspect the actual resource and restore verified connection settings
before retrying an ambiguous failure. An immediate readback failure can also
reflect delayed persistence; investigate it before retrying rather than assuming
the database was not created.

## Database engine declarations

Forge checks the existing server's database engine before provisioning. `mysql`
accepts MySQL or MariaDB, `mariadb` requires MariaDB, and `postgres`, `postgresql`
or `pgsql` require PostgreSQL. A declaration does not change the server engine.

Ploi accepts those SQL declaration labels and rejects unsupported types such as
Redis, SQLite and MongoDB before API access. Matching the actual existing-server
engine is still unverified; accepted syntax is not proof of engine compatibility.
Confirm that the selected server supports the database type you intend to use.

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
