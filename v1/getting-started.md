---
description: "Install the CLI, create a first shipper.yml, and run validate, plan, and apply in the intended order."
section: "Getting Started"
order: 10
---

# Getting Started

Deploy an nginx container to a disposable Coolify project with an existing production environment. Run these commands from a clean directory with PHP 8.3 or newer and Composer 2.

## Install

```bash
composer init --name=local/shipper-deployment --no-interaction
composer config repositories.provider-coolify git https://github.com/shippercli/provider-coolify.git
composer require shippercli/cli:1.0.10 shippercli/provider-coolify:1.0.9
```

## Configure

Set `COOLIFY_URL`, `COOLIFY_API_TOKEN`, `COOLIFY_SERVER_UUID`, and `COOLIFY_PROJECT_UUID` in your shell. Use a temporary API token scoped to the disposable project. Save this file as `shipper.yml`.

```yaml
providers:
  coolify:
    url: ${COOLIFY_URL}
    api_token: ${COOLIFY_API_TOKEN}
    server_uuid: ${COOLIFY_SERVER_UUID}
    project_uuid: ${COOLIFY_PROJECT_UUID}
projects:
  shipper-reusable-fixture:
    provider: coolify
    ssl:
      enabled: false
    profiles:
      production:
        domain: shipper-reusable-fixture.test
        coolify:
          environment_name: production
          source_type: docker_image
          docker_image: nginx
          docker_registry_image_tag: alpine
          ports_exposes: "80"
```

## Validate, plan, and deploy

```bash
vendor/bin/shipper validate
vendor/bin/shipper plan shipper-reusable-fixture --profile=production
vendor/bin/shipper apply shipper-reusable-fixture --profile=production
```

Review the plan before confirming apply. Replace `COOLIFY_PROXY_ADDRESS` below with the address of the Coolify HTTP proxy and check for the nginx welcome page.

```bash
curl -fsS -H 'Host: shipper-reusable-fixture.test' http://COOLIFY_PROXY_ADDRESS/ |
  grep -F 'Welcome to nginx'
```

Clean up the fixture, then revoke the temporary token.

```bash
vendor/bin/shipper destroy shipper-reusable-fixture --profile=production
```

See the [provider catalog](/providers) for other providers and their installation instructions.
