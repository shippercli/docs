---
description: "What `shipper plan` previews, how to use it safely, and how to read create, reuse, and destroy intent."
section: "CLI Commands"
order: 61
---

# Plan

`shipper plan` previews what Shipper expects to do.

## When to Run It

- before apply in production
- before destroy in temporary environments
- after changing lifecycle-related configuration

## Example

```bash
php shipper plan --project api --profile production
```

## What to Look For

- site create vs reuse
- database create vs reuse
- server create vs reuse
- any destroy behavior implied by the selected flow

## Related Pages

- [Resource Lifecycle](./RESOURCE_LIFECYCLE.md)
- [Site Lifecycle](./SITES.md)
- [Database Lifecycle](./DATABASES.md)
- [Server Lifecycle](./SERVER_LIFECYCLE.md)

## Failure Modes

- skipping plan before destructive work: use it to confirm destroy intent first
- misreading temporary resource ownership: review lifecycle docs before apply or destroy
