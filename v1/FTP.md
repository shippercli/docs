---
description: "Install and use the FTP/FTPS provider to deploy prebuilt files, retry interrupted uploads, and remove only tracked files."
section: "Deployment Features"
order: 31
---

# FTP / FTPS Deployment

[FTP provider v1.0.0](https://github.com/shippercli/provider-ftp/releases/tag/v1.0.0)
uploads prebuilt local files to an existing FTP account. Its hosting compatibility
is marked beta: the server must support MLSD listings and replacing an existing
file by rename. PHP 8.3+ with `ext-curl` is required.

## Installation

Install the Composer version of Shipper with the provider's public Git repository:

```bash
composer config repositories.shipper-ftp git https://github.com/shippercli/provider-ftp.git
composer require 'shippercli/cli:^1.0.10' 'shippercli/provider-ftp:^1.0'
```

This installation does not require Packagist registration. Use `vendor/bin/shipper`
for a local installation. For a global installation, use `composer global config`
and `composer global require` with the same arguments, then run `shipper`.

## Configuration

Build your application locally before applying; for a static application this
might be `npm ci && npm run build` with output in `dist`.

```yaml
providers:
  ftp:
    host: ftp.example.com
    port: 21
    protocol: ftps
    username: "${FTP_USERNAME}"
    password: "${FTP_PASSWORD}"
    timeout: 30

projects:
  website:
    provider: ftp
    path: .
    profiles:
      production:
        branch: main
        ftp:
          local_path: dist
          remote_path: /public_html/website
          exclude:
            - private
            - debug.txt
```

`ftps` is the default: explicit TLS protects both control and data connections,
and certificates and hostnames are always verified. Set `ca_file` under
`providers.ftp` for a readable private CA PEM bundle. Implicit FTPS and SFTP are
unsupported. Choosing `protocol: ftp` sends files and credentials in plaintext.

`local_path` defaults to `.` relative to the project's existing `path` and must
remain inside that project. The provider uploads local files; `branch` does not
check out Git or select a remote source. `exclude` accepts exact relative paths
and directory prefixes, without globs. Git metadata, `.github`, `node_modules`,
`.env*`, `auth.json`, `shipper.yml`, `shipper.yaml`, PHPUnit caches and reserved
`.shipper-ftp*` files are always excluded. Review artifacts for other secrets.
Symlinks, special files and empty artifacts are rejected; empty directories are
not uploaded.

`remote_path` must be a dedicated absolute non-root directory inside the FTP
account's filesystem namespace. A chrooted account sees its own root as `/`.
The first deployment requires an empty directory; missing directories are
created. Existing websites are not adopted. The account needs list, read,
write, rename and delete permissions.

## Deployment and Cleanup

```bash
vendor/bin/shipper validate
vendor/bin/shipper plan website --profile=production
vendor/bin/shipper apply website --profile=production
vendor/bin/shipper status website --profile=production
vendor/bin/shipper destroy website --profile=production
```

Plan inspects the local artifact without connecting to the server. Apply checks
the project/profile/path ownership manifest, journals the previous and desired
file list, uploads temporary files, renames them into place and removes stale
tracked files. Status reads the saved deployment state; it does not check
application health or detect manual edits.

Destroy removes tracked files and their upload temporary files. It preserves
unrelated files and all directories, including the deployment root. Tracked
files remain deployment-owned even after manual edits: apply can overwrite
them and destroy deletes them.

After an interrupted upload, retry apply or use destroy to remove tracked
partial files. An invalid initial journal can require manual inspection and
cleanup. Do not delete an existing manifest to bypass ownership checks.

## Operational Limits

- Run only one deployment or destroy at a time per directory. There is no
  concurrency lock or atomic multi-file release; failure can leave mixed versions.
- No remote build, shell, environment generation, migrations, databases, DNS,
  domain management, website certificate provisioning, process management,
  runtime logs, rollback or automatic preview environments are available.
  Unsupported configured resources and runtime settings fail validation.
- Reserve `.shipper-ftp*` names and block HTTP access to them in the web server;
  they contain filenames and deployment state, though no credentials.
- Files are buffered individually in PHP memory. Size its memory limit for the
  largest artifact file. File permissions, ownership and timestamps are not preserved.

Read the provider's [operations and recovery guide](https://github.com/shippercli/provider-ftp/blob/v1.0.0/docs/operations.md)
and [capability table](https://github.com/shippercli/provider-ftp/blob/v1.0.0/docs/capabilities.md)
for full filesystem assumptions and recovery details.
