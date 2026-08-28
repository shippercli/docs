---
description: "How the CLI is packaged, released, and distributed across local installs, binaries, and GitHub Actions."
section: "Tooling"
order: 80
---

# Build and Distribution

This page explains how the Shipper CLI is packaged and distributed.

## Distribution Modes

- local development from source
- downloadable binary releases
- reusable GitHub Action usage in CI

## Local Build

```bash
composer build
```

## Release Flow

A tagged release can build and publish versioned binaries through CI.

## Failure Modes

- assuming the action and local CLI are versioned together without pinning: use explicit tags in CI
- using unpinned references for production workflows: prefer fixed release refs
