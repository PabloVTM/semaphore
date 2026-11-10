# Semaphore UI (fork)

A modern web UI to run Ansible playbooks, Terraform / OpenTofu / Terragrunt code, Bash and PowerShell scripts: projects, task templates, schedules, inventories, secrets, access control and notifications on failed tasks.

This is a fork of [semaphoreui/semaphore](https://github.com/semaphoreui/semaphore) by [Denis Gukov](https://github.com/fiftin), maintained at [PabloVTM/semaphore](https://github.com/PabloVTM/semaphore) with the PabloSystems theme shared with the other PabloVTM tools: Deep Sage Green `#4B5A50`, Warm Ivory `#F1EEE3` and Vibrant Vermillion Red `#F20B0B` for details, system monospace fonts and a `pablo@systems:~/semaphore` prompt on the sign-in page and in the navigation drawer, in light and dark modes. It is the open-source (MIT) edition: features of Semaphore PRO and Enterprise stay unavailable.

## Image

The server image is published to GitHub Container Registry for `linux/amd64` and `linux/arm64`:

| Tag | Content |
| --- | --- |
| `ghcr.io/pablovtm/semaphore:latest` | Latest build of `master`. |
| `ghcr.io/pablovtm/semaphore:YYYY-MM-DD` | Dated version, e.g. `2026-11-10`. Each build upgrades the Alpine packages, so a new dated version also brings the latest Alpine security fixes. |

It bundles Ansible 13.8, OpenTofu 1.13, Terraform 1.17 and Terragrunt 0.78 (Terragrunt 1.x no longer forwards the `workspace` commands Semaphore runs).

## Quick start

```yaml
services:
  semaphore:
    image: ghcr.io/pablovtm/semaphore:latest
    restart: unless-stopped
    ports:
      - 3000:3000
    environment:
      SEMAPHORE_DB_DIALECT: sqlite
      SEMAPHORE_ADMIN: admin
      SEMAPHORE_ADMIN_PASSWORD: changeme
      SEMAPHORE_ADMIN_NAME: Admin
      SEMAPHORE_ADMIN_EMAIL: admin@localhost
      # recommended: encrypts the stored keys and secrets. Generate it once with
      # `head -c32 /dev/urandom | base64` and keep it with your backups.
      # SEMAPHORE_ACCESS_KEY_ENCRYPTION: <your key>
    volumes:
      - semaphore-data:/var/lib/semaphore
      - semaphore-config:/etc/semaphore

volumes:
  semaphore-data:
  semaphore-config:
```

or

```bash
docker run -d --name semaphore -p 3000:3000 \
  -e SEMAPHORE_DB_DIALECT=sqlite \
  -e SEMAPHORE_ADMIN=admin \
  -e SEMAPHORE_ADMIN_PASSWORD=changeme \
  -e SEMAPHORE_ADMIN_NAME=Admin \
  -e SEMAPHORE_ADMIN_EMAIL=admin@localhost \
  ghcr.io/pablovtm/semaphore:latest
```

Then open http://localhost:3000 and sign in as `admin`. The container reports its health on `/api/ping`. Change the admin password after the first sign-in, and put a reverse proxy with TLS in front of Semaphore before exposing it: it runs code on your infrastructure.

MySQL, MariaDB and PostgreSQL are supported too (`SEMAPHORE_DB_DIALECT`, `SEMAPHORE_DB_HOST`, `SEMAPHORE_DB_USER`, `SEMAPHORE_DB_PASS`, `SEMAPHORE_DB`); the compose files in [deployment/compose](deployment/compose) combine the server with each of them.

## Configuration

Semaphore reads its settings from `config.json` (`/etc/semaphore` in the container) or from `SEMAPHORE_*` environment variables. All options are described in [config.schema.yaml](config.schema.yaml); the [upstream documentation](https://docs.semaphoreui.com) covers LDAP / OIDC sign-in, runners, notifications and the API.

## Releasing

Push a date tag to publish a dated version (and `latest`):

```bash
git tag 2026-11-10
git push origin 2026-11-10
```

The [build-test-publish](.github/workflows/build-test-publish.yml) workflow lints and tests the web app and the Go code, builds the image from the latest Alpine base with no cache, smoke tests it (SQLite, admin sign-in, the theme in the bundle, the bundled tools, no pending Alpine updates), then pushes the multi-arch image. Pushes to `master` publish `latest`.

## Building

```bash
docker build -f deployment/docker/server/Dockerfile -t semaphore .
```

## Development

Requires Go 1.26, Node.js 24 and [Task](https://taskfile.dev).

```bash
task deps     # Go modules and web dependencies
task build    # web bundle (embedded in the binary) and bin/semaphore
task test     # web unit tests and Go tests
```

The web app alone runs with `npm run serve` in `web/` (it proxies the API of a running server). A [dev container](.devcontainer) is available too (default user `admin` / `changeme`).

## Credits

Semaphore UI is created by [Denis Gukov](https://github.com/fiftin) and the [contributors](https://github.com/semaphoreui/semaphore/graphs/contributors) of [semaphoreui/semaphore](https://github.com/semaphoreui/semaphore), originally Ansible Semaphore by Castaway Labs. If you rely on it, consider [Semaphore PRO](https://semaphoreui.com/pro) to support the project. This fork is maintained by [PabloVTM](https://github.com/PabloVTM).

## License

[MIT](LICENSE) © Denis Gukov, Castaway Labs LLC.
