# n8n

Production-ready Docker Compose stack for n8n using PostgreSQL, Redis, and Queue Mode.

This stack is based on a real-world production deployment and has been adapted to follow the standards of this repository.

The goal is to provide a portable, reproducible, well-documented, and maintainable deployment that can be installed on a clean Docker host without relying on environment-specific configurations.

## Features

- Production-ready configuration
- PostgreSQL database backend
- Redis queue backend
- Queue Mode architecture
- Dedicated worker container
- Persistent bind-mounted storage
- Portable directory structure
- Secure by default
- Version-pinned container images
- Repository-standard layout

## Architecture

```text
                 +----------------------+
                 |         n8n          |
                 |   Main Application   |
                 +----------+-----------+
                            |
          +-----------------+-----------------+
          |                                   |
+----------------------+         +----------------------+
|     PostgreSQL       |         |        Redis         |
| Persistent Database  |         |    Queue Backend     |
+----------------------+         +----------+-----------+
                                            |
                                            |
                                  +---------v----------+
                                  |     n8n Worker     |
                                  | Workflow Execution |
                                  +--------------------+
```

## Requirements

Before deploying this stack, ensure the following requirements are met:

- Docker Engine 28 or newer
- Docker Compose v2

## Directory Structure

```text
n8n/
├── docker-compose.yml
├── .env.example
├── .gitignore
└── README.md
```

After the first deployment, Docker will automatically create the required persistent directories.

Typical directory layout:

```text
n8n/
├── n8n/
├── pgdata/
└── redis/
```

## Quick Start

Copy the example environment file.

```bash
cp .env.example .env
```

Edit the `.env` file and replace every `change-me` value with a securely generated secret.

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
| `N8N_VERSION` | Yes | n8n image version |
| `POSTGRES_VERSION` | Yes | PostgreSQL image version |
| `REDIS_VERSION` | Yes | Redis image version |
| `TZ` | Yes | Time zone |
| `N8N_HOST` | Yes | Public URL hostname |
| `N8N_BASIC_AUTH_USER` | Yes | Basic Authentication username |
| `N8N_BASIC_AUTH_PASSWORD` | Yes | Basic Authentication password |
| `N8N_ENCRYPTION_KEY` | Yes | Encryption key used to protect credentials |
| `N8N_SMTP_HOST` | No | SMTP server hostname |
| `N8N_SMTP_PORT` | No | SMTP server port |
| `N8N_SMTP_SSL` | No | Enable SMTP SSL |
| `N8N_SMTP_USER` | No | SMTP username |
| `N8N_SMTP_PASS` | No | SMTP password |
| `N8N_SMTP_SENDER` | No | Sender email address |
| `POSTGRES_DB` | Yes | PostgreSQL database name |
| `POSTGRES_USER` | Yes | PostgreSQL username |
| `POSTGRES_PASSWORD` | Yes | PostgreSQL password |

## Generating Secrets

Generate a secure Basic Authentication password.

```bash
openssl rand -base64 32
```

Generate an encryption key.

```bash
openssl rand -hex 32
```

Generate a secure PostgreSQL password.

```bash
openssl rand -base64 32
```

## Backup

The following directories should be included in your backup strategy:

- `n8n/`
- `pgdata/`
- `redis/`

## Restore

Restore the persistent directories before starting the containers.

After restoring the data, start the stack using:

```bash
docker compose up -d
```

## Update

Pull the latest supported container images.

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

## Design Decisions

This stack intentionally follows the engineering standards defined for this repository.

Key design decisions include:

- PostgreSQL is used instead of SQLite.
- Redis is used for Queue Mode.
- Workflow execution is handled by a dedicated worker.
- Persistent data uses bind mounts.
- Image versions are pinned instead of using `latest`.
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

View container logs.

```bash
docker compose logs
```

View logs for the main application.

```bash
docker compose logs n8n
```

View logs for the worker.

```bash
docker compose logs worker
```

Restart the stack.

```bash
docker compose restart
```

## License

This stack is distributed under the MIT License.

See the repository `LICENSE` file for details.
