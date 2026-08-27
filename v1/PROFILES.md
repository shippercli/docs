---
description: "How deployment environments are modeled, including branch selection, domains, previews, databases, and infrastructure."
section: "Configuration"
order: 23
---

# Profiles

Profiles model environments such as `production`, `staging`, and `preview`.

## Shape

```yaml
projects:
  api:
    profiles:
      production:
        branch: main
        domain: "api.example.com"

      staging:
        branch: develop
        domain: "api.example-test.com"
```

## Common Fields

### `branch`

The branch to deploy for the environment.

### `domain`

The hostname for the environment.

Database declarations belong at the project level under `projects.<name>.databases`.
Use profile interpolation in database names when each environment needs distinct resources.

### `infrastructure`

Environment-specific infrastructure behavior such as server reuse or provisioning.

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
      production:
        branch: main
        domain: "api.example.com"

      preview:
        branch: "${GITHUB_HEAD_REF}"
        domain: "api-preview-${GITHUB_PR_NUMBER}.example.com"
        infrastructure:
          server:
            mode: create
            name: "api-preview-${GITHUB_PR_NUMBER}"
            cleanup: destroy
```

## Related Capability Pages

- [Database Lifecycle](./DATABASES.md)
- [Server Lifecycle](./SERVER_LIFECYCLE.md)
- [PR Preview Environments](./PR_PREVIEWS.md)

## Failure Modes

- putting profile fields at project level: move them into the correct environment block
- reusing production domains in non-production profiles: keep domains unique by environment
- mixing preview variables into long-lived environments: only use dynamic preview naming where intended
