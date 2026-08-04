# Paperless-ngx

Production-ready Docker Compose stack for Paperless-ngx using PostgreSQL and Redis.

This stack is based on a real-world production deployment and has been adapted to follow the standards of this repository.

The goal is to provide a portable, reproducible, well-documented, and maintainable deployment that can be installed on a clean Docker host without relying on environment-specific configurations.

## Features

- Production-ready configuration
- PostgreSQL database backend
- Redis for task queue and caching
- OCR support
- Persistent bind-mounted storage
- Portable directory structure
- Secure by default
- Version-pinned container images
- Repository-standard layout

## Architecture

```text
                 +----------------------+
                 |    Paperless-ngx     |
                 +----------+-----------+
                            |
          +-----------------+-----------------+
          |                                   |
+----------------------+         +----------------------+
|     PostgreSQL       |         |        Redis         |
| Persistent Database  |         | Queue & Cache        |
+----------------------+         +----------------------+
```

## Requirements

Before deploying this stack, ensure the following requirements are met:

- Docker Engine 28 or newer
- Docker Compose v2

## Directory Structure

```text
paperless-ngx/
├── docker-compose.yml
├── .env.example
├── .gitignore
└── README.md
```

After the first deployment, Docker will automatically create the required persistent directories.

Typical directory layout:

```text
paperless-ngx/
├── consume/
├── data/
├── export/
├── media/
├── pgdata/
└── redis/
```

## Quick Start

Copy the example environment file.

```bash
cp .env.example .env
```

Edit the `.env` file and replace every `change-me` value with securely generated secrets.

Validate the Docker Compose configuration.

```bash
docker compose config
```

Start the stack.

```bash
docker compose up -d
```

Verify that all containers are healthy.

```bash
docker compose ps
```

## Environment Variables

| Variable | Required | Description |
|-----------|:--------:|-------------|
| `PAPERLESS_VERSION` | Yes | Paperless-ngx image version |
| `POSTGRES_VERSION` | Yes | PostgreSQL image version |
| `REDIS_VERSION` | Yes | Redis image version |
| `TZ` | Yes | Time zone |
| `PAPERLESS_URL` | Yes | Public URL of the Paperless instance |
| `PAPERLESS_SECRET_KEY` | Yes | Django secret key |
| `PAPERLESS_OCR_LANGUAGE` | Yes | Default OCR language |
| `PAPERLESS_OCR_LANGUAGES` | Yes | Additional OCR languages |
| `POSTGRES_DB` | Yes | PostgreSQL database name |
| `POSTGRES_USER` | Yes | PostgreSQL username |
| `POSTGRES_PASSWORD` | Yes | PostgreSQL password |

## Generating Secrets

Generate a Paperless secret key.

```bash
openssl rand -hex 64
```

Generate a secure PostgreSQL password.

```bash
openssl rand -base64 32
```

## Backup

Back up the following persistent directories regularly:

- `data/`
- `media/`
- `export/`
- `consume/`
- `pgdata/`
- `redis/`

A repository-standard backup script will be added in a future revision.

## Restore

Restore the persistent directories before starting the stack.

After restoring the data, start the services using:

```bash
docker compose up -d
```

A repository-standard restore script will be added in a future revision.

## Update

Pull the repository-supported container images.

```bash
docker compose pull
```

Recreate the containers.

```bash
docker compose up -d
```

Verify that all services are running correctly.

```bash
docker compose ps
```

## Design Decisions

This stack intentionally follows the engineering standards defined for this repository.

Key design decisions include:

- PostgreSQL is used instead of SQLite.
- Redis provides queue management and caching.
- Persistent data uses bind mounts.
- Image versions are managed through `.env.example`.
- No `container_name` values are defined.
- No external Docker networks are required.
- Environment-specific values are stored in `.env`.
- Production defaults remain inside `docker-compose.yml`.

The objective is to keep the stack portable, reproducible, and easy to maintain.

## Troubleshooting

Check container status.

```bash
docker compose ps
```

View logs from all services.

```bash
docker compose logs
```

View logs from Paperless-ngx only.

```bash
docker compose logs paperless
```

Restart the stack.

```bash
docker compose restart
```

## License

This stack is distributed under the MIT License.

See the repository `LICENSE` file for details.
