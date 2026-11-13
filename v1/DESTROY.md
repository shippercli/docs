---
description: "What `shipper destroy` removes, how destroy intent should be reviewed, and where cleanup boundaries come from."
section: "CLI Commands"
order: 63
---

# Destroy

`shipper destroy` tears down the resources associated with a project profile.

## Example

```bash
vendor/bin/shipper destroy api --profile=preview
```

## Typical Use Cases

- PR preview cleanup
- teardown of temporary staging environments
- explicit removal of managed resources

## Review Before Running

- review the target shown by `destroy` before confirming
- confirm whether server cleanup is `destroy`, `retain`, or `manual`
- confirm whether preview databases and sites are uniquely scoped to the target environment

## Related Pages

- [PR Preview Environments](./PR_PREVIEWS.md)
- [Server Lifecycle](./SERVER_LIFECYCLE.md)
- [Resource Lifecycle](./RESOURCE_LIFECYCLE.md)

## Failure Modes

- destroying a long-lived environment by selecting the wrong profile
- assuming unmanaged resources are safe to remove
- using `--force` bypasses the confirmation prompt
