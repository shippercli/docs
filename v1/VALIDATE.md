---
description: "What `shipper validate` checks, when to run it, and how to interpret failures."
section: "CLI Commands"
order: 60
---

# Validate

`shipper validate` checks configuration before any deployment work happens.

## When to Run It

- before the first deployment
- in CI before plan or apply
- after changing provider, project, or profile configuration

## Example

```bash
php shipper validate
```

## What It Should Catch

- invalid configuration shape
- missing required fields
- mismatched provider references

## Related Pages

- [Configuration Reference](./CONFIGURATION.md)
- [Providers](./PROVIDERS.md)
- [Profiles](./PROFILES.md)

## Failure Modes

- missing environment variables: export the provider credentials before validating
- provider-specific field mismatch: compare against the provider page
