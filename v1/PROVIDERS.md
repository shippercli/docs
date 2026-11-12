---
description: "How the providers block is structured, how projects reference providers, and where provider-specific options belong."
section: "Configuration"
order: 21
---

# Providers

The `providers` block defines provider credentials and provider-specific settings.

## Shape

```yaml
providers:
  hosting:
    api_key: "${HOSTING_API_KEY}"
```

Projects reference a provider by key:

```yaml
projects:
  api:
    provider: hosting
```

## What Belongs Here

- credentials
- API endpoints or hosts when the provider requires them
- provider-specific identifiers that are shared across environments

## What Does Not Belong Here

- profile branch selection
- profile domain names
- preview-only naming
- per-environment infrastructure decisions

Those belong in [Profiles](./PROFILES.md).

## Capability Differences

Provider-specific fields and support levels are documented on provider pages:

- [Ploi](/providers/ploi)
- [Laravel Forge](/providers/forge)
- [cPanel](/providers/cpanel)
- [EasyPanel](/providers/easypanel)
- [Coolify provider and installation instructions](https://github.com/shippercli/provider-coolify)

Check the [published provider verification matrix](https://github.com/shippercli/provider-gap-matrix#distribution-and-verification) before choosing a provider. It lists the installation path for each release, the CLI operations each provider exposes, and the operations verified on live targets.

## Failure Modes

- wrong credential variable names: match the provider page exactly
- using one provider config for a different provider plugin: keep keys and expectations aligned
