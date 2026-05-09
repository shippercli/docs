# Getting Started

## Installation

Install Shipper via Composer:

```bash
composer require ulties/shipper
```

## Configuration

Create a `shipper.yml` file in your project root:

```yaml
providers:
  ploi:
    api_key: "${PLOI_API_KEY}"
    server_id: "105556"

projects:
  myapp:
    provider: ploi
    domain: "example.com"
    profiles:
      production:
        branch: main
```

## Validate

Always validate before deploying:

```bash
php shipper validate
```

## Plan

Preview what would be deployed:

```bash
php shipper plan --project myapp --profile production
```

## Apply

Deploy to production:

```bash
php shipper apply --project myapp --profile production
```
