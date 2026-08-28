---
description: "Install the CLI, create a first shipper.yml, and run validate, plan, and apply in the intended order."
section: "Getting Started"
order: 10
---

# Getting Started

This page walks through the first successful deployment flow.

## Install

```bash
composer require ulties/shipper
```

## Create a First Configuration

```yaml
providers:
  hosting:
    api_key: "${HOSTING_API_KEY}"

projects:
  api:
    provider: hosting
    profiles:
      production:
        branch: main
        domain: "api.example.com"
```

Provider-specific keys belong on the provider pages:

- [Ploi](/providers/ploi)
- [Laravel Forge](/providers/forge)
- [cPanel](/providers/cpanel)
- [EasyPanel](/providers/easypanel)

## Run the CLI in Order

1. [Validate](./VALIDATE.md) the configuration
2. [Plan](./PLAN.md) the deployment
3. [Apply](./APPLY.md) the deployment

## Validate

```bash
php shipper validate
```

## Plan

```bash
php shipper plan --project api --profile production
```

## Apply

```bash
php shipper apply --project api --profile production
```

## Failure Modes

- missing provider credentials: set the required environment variables for the chosen provider
- unknown provider key: ensure `projects.<name>.provider` matches a key under `providers`
- incomplete profile: add at least the branch and domain for the first environment

## Read Next

- [Concepts](./CONCEPTS.md)
- [Configuration Reference](./CONFIGURATION.md)
- [GitHub Actions Workflows](./GITHUB_ACTIONS.md)
