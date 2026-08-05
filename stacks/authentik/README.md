# Authentik

Production-ready Docker Compose stack for Authentik using PostgreSQL and Redis.

This stack is based on a real-world production deployment and has been adapted to follow the engineering standards of this repository.

The goal is to provide a portable, reproducible, well-documented, and maintainable deployment that can be installed on a clean Docker host without relying on environment-specific configurations.

## Features

- Production-ready configuration
- Official Authentik deployment architecture
- PostgreSQL database backend
- Redis cache and message broker
- Dedicated server and worker containers
- Persistent bind-mounted storage
- Portable directory structure
- Secure by default
- Version-pinned container images
- Repository-standard layout

## Architecture

```text
                    +----------------------+
                    |   Authentik Server   |
                    +----------+-----------+
                               |
             +-----------------+-----------------+
             |                                   |
    +--------+--------+                 +--------+--------+
    |   PostgreSQL    |                 |      Redis      |
    | Persistent Data |                 | Cache / Broker  |
    +-----------------+                 +--------+--------+
                                                 |
                                        +--------+--------+
                                        | Authentik Worker|
                                        +-----------------+
```

## Requirements

Before deploying this stack, ensure the following requirements are met:

- Docker Engine 28 or newer
- Docker Compose v2

## Directory Structure

```text
authentik/
├── docker-compose.yml
├── .env.example
├── .gitignore
└── README.md
```

After the first deployment, Docker will automatically create the required persistent directories.

Typical directory layout:

```text
authentik/
├── media/
├── postgresql/
└── redis/
```

## Quick Start

Copy the example environment file.

```bash
cp .env.example .env
```

Edit the `.env` file and replace every `change-me` value with securely generated secrets.

Start the stack.

```bash
docker compose up -d
```

Verify that all containers are running.

```bash
docker compose ps
```

For local testing only, you may temporarily publish the Authentik HTTP port to access the initial setup interface.

Remove any temporary port mappings before using this stack in production.

## Environment Variables

| Variable | Required | Description |
|-----------|:--------:|-------------|
| `AUTHENTIK_VERSION` | Yes | Authentik image version |
| `POSTGRES_VERSION` | Yes | PostgreSQL image version |
| `REDIS_VERSION` | Yes | Redis image version |
| `TZ` | Yes | Time zone |
| `PG_USER` | Yes | PostgreSQL username |
| `PG_DB` | Yes | PostgreSQL database name |
| `PG_PASS` | Yes | PostgreSQL password |
| `AUTHENTIK_SECRET_KEY` | Yes | Authentik secret key |
| `AUTHENTIK_EMAIL__HOST` | No | SMTP server hostname |
| `AUTHENTIK_EMAIL__PORT` | No | SMTP server port |
| `AUTHENTIK_EMAIL__USERNAME` | No | SMTP username |
| `AUTHENTIK_EMAIL__PASSWORD` | No | SMTP password |
| `AUTHENTIK_EMAIL__USE_TLS` | No | Enable STARTTLS |
| `AUTHENTIK_EMAIL__USE_SSL` | No | Enable SSL |
| `AUTHENTIK_EMAIL__TIMEOUT` | No | SMTP timeout |
| `AUTHENTIK_EMAIL__FROM` | No | Sender email address |

## Generating Secrets

Generate a secure PostgreSQL password.

```bash
openssl rand -base64 36
```

Generate a secure Authentik secret key.

```bash
openssl rand -base64 60
```

Copy the generated values into your `.env` file before starting the stack.

## Backup

The following directories should be included in your backup strategy:

- `media/`
- `postgresql/`
- `redis/`

The PostgreSQL database contains all Authentik configuration, users, applications, policies, providers, flows, and most persistent data.

The `media` directory stores uploaded assets such as icons, backgrounds, and other filesystem content when local storage is used.

The Redis directory contains cached data and queued tasks. While it is less critical than PostgreSQL, backing it up helps preserve pending background jobs and reduces recovery time.

## Restore

Restore the persistent directories before starting the containers.

Start the stack.

```bash
docker compose up -d
```

Verify that all containers are healthy.

```bash
docker compose ps
```

If restoring from a PostgreSQL dump instead of the bind-mounted data directory, restore the database before allowing Authentik to serve requests.

## Update

Review the release notes before upgrading.

Update the image versions in `.env`.

Pull the new images.

```bash
docker compose pull
```

Recreate the containers.

```bash
docker compose up -d
```

Verify the deployment.

```bash
docker compose ps
```

Authentik recommends reviewing the release notes before every upgrade because newer releases may introduce migration steps or updated deployment recommendations.

## Design Decisions

This stack intentionally follows the engineering standards defined for this repository.

Key design decisions include:

- The official Authentik deployment architecture is preserved.
- PostgreSQL is used as the database backend.
- Redis is used for caching and background task processing.
- Dedicated Server and Worker containers are used.
- Persistent data uses bind mounts instead of named volumes.
- Image versions are pinned instead of using `latest`.
- No `container_name` values are defined.
- No external Docker networks are required.
- No ports are published by default.
- Environment-specific values are stored in `.env`.
- Production defaults remain inside `docker-compose.yml`.
- Docker socket mounting is intentionally omitted.

### Why are the Docker socket and `user: root` omitted?

The official Authentik Docker Compose deployment mounts the Docker socket into the worker container and runs the worker as `root` to enable automatic deployment and lifecycle management of Docker Outposts.

This repository intentionally omits both the Docker socket mount and the `user: root` directive because Docker-managed Outposts are outside the scope of this stack.

Removing the Docker socket prevents the worker from accessing the host Docker API, while omitting `user: root` follows the principle of least privilege by avoiding unnecessary root execution.

If you plan to use Docker-managed Outposts, consult the official Authentik documentation before enabling these options. Consider using a Docker Socket Proxy instead of exposing the Docker API directly to reduce the associated security risks.

### Why are no ports published?

This repository does not assume any reverse proxy, network topology, or deployment architecture.

For local testing, you may temporarily publish Authentik's HTTP port to complete the initial setup.

Those temporary port mappings should be removed before deploying the stack in production.

## Troubleshooting

Check container status.

```bash
docker compose ps
```

View all logs.

```bash
docker compose logs
```

View the Authentik server logs.

```bash
docker compose logs server
```

View the Authentik worker logs.

```bash
docker compose logs worker
```

Restart the stack.

```bash
docker compose restart
```

Validate the Compose configuration.

```bash
docker compose config
```

## Tested With

This stack has been validated using:

- Ubuntu 26.04 LTS
- Docker Engine 28
- Docker Compose v2
- Authentik 2026.5.6
- PostgreSQL 16
- Redis 8

## License

This stack is distributed under the MIT License.

See the repository `LICENSE` file for details.
