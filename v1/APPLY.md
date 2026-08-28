---
description: "What `shipper apply` executes, how it uses the selected profile, and what to verify before running it."
section: "CLI Commands"
order: 62
---

# Apply

`shipper apply` executes the deployment flow for a project profile.

## Example

```bash
php shipper apply --project api --profile production
```

## What Apply Uses

- the selected project
- the selected profile
- provider credentials from the environment
- lifecycle decisions implied by site, database, and server configuration

## Run Apply After

1. [Validate](./VALIDATE.md)
2. [Plan](./PLAN.md)

## Failure Modes

- applying the wrong profile: verify the environment name before running the command
- missing secrets in CI: ensure the provider credentials are exported as environment variables
- assuming provider behavior instead of checking support: confirm the provider page first
