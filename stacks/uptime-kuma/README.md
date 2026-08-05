# Uptime Kuma

Production-ready Docker Compose stack for Uptime Kuma.

This stack is based on a real-world production deployment and has been adapted to follow the engineering standards of this repository.

The goal is to provide a portable, reproducible, well-documented, and maintainable deployment that can be installed on a clean Docker host without relying on environment-specific configurations.

## Features

- Production-ready configuration
- Version-pinned container image
- Flexible database backend selection
- Persistent bind-mounted storage
- Portable directory structure
- Secure by default
- Minimal Docker Compose configuration
- Repository-standard layout

## Architecture

```text
            +----------------------+
            |    Uptime Kuma       |
            +----------+-----------+
                       |
                Database Backend
                       |
     +-----------------+-----------------+
     |                 |                 |
Embedded MariaDB   MariaDB/MySQL     SQLite
```

## Requirements

Before deploying this stack, ensure the following requirements are met:

- Docker Engine 28.0 or newer
- Docker Compose v2

## Directory Structure

```text
uptime-kuma/
├── docker-compose.yml
├── .env.example
├── .gitignore
└── README.md
```

After the first deployment, Docker will automatically create the required persistent directory.

Typical directory layout:

```text
uptime-kuma/
├── data/
├── docker-compose.yml
├── .env.example
├── .gitignore
└── README.md
```

## Quick Start

Copy the example environment file.

```bash
cp .env.example .env
```

Validate the Docker Compose configuration.

```bash
docker compose config -q
```

Start the stack.

```bash
docker compose up -d
```

Verify that the container is running.

```bash
docker compose ps
```

Complete the initial setup by opening the Uptime Kuma web interface.

During the setup wizard, select the preferred database backend and create the administrator account.

## Environment Variables

| Variable | Required | Description |
|-----------|:--------:|-------------|
| `UPTIME_KUMA_VERSION` | Yes | Uptime Kuma image version |
| `TZ` | Yes | Time zone |

## Generating Secrets

This stack does not require any secrets during deployment.

No database credentials are required when using Embedded MariaDB or SQLite.

The administrator account is created during the initial web-based setup.

## Backup

Back up the following persistent directory regularly:

- `data/`

This directory contains all persistent application data, including:

- Database files
- Monitor configuration
- Notification settings
- Status pages
- User accounts
- Application configuration

The exact database files depend on the database backend selected during the initial setup.

Back up the `data/` directory while the container is stopped to ensure database consistency.

## Restore

Restore the `data/` directory.

After restoring the data, start the stack:

```bash
docker compose up -d
```

Verify that the service starts successfully:

```bash
docker compose ps
```

The application will automatically use the previously configured data stored in the `data/` directory.

## Update

Pull the repository-supported container image.

```bash
docker compose pull
```

Recreate the container.

```bash
docker compose up -d --force-recreate
```

Verify that the service is running correctly.

```bash
docker compose ps
```

## Design Decisions

This stack intentionally follows the engineering standards defined for this repository.

Key design decisions include:

- The Docker Compose stack does not enforce a database backend.
- Database selection is performed during the initial setup wizard.
- Persistent data uses bind mounts.
- Image versions are managed through `.env.example`.
- No `container_name` values are defined.
- No ports are published by default.
- No reverse proxy configuration is included.
- No external Docker networks are required.
- Environment-specific values are stored in `.env`.
- Production defaults remain inside `docker-compose.yml`.
- Docker socket mounting is intentionally omitted from the default configuration.

Uptime Kuma supports multiple database backends without requiring changes to the Docker Compose stack.

During the initial setup, you can choose one of the following:

- Embedded MariaDB
- External MariaDB/MySQL
- SQLite

The Docker Compose stack remains identical regardless of the selected database backend.

The objective is to keep the stack portable, reproducible, secure, and easy to maintain.

### Optional: Docker Container Monitoring

Uptime Kuma supports monitoring Docker containers by connecting to the Docker daemon.

If Docker container monitoring is required, mount the Docker socket into the container:

```yaml
volumes:
  - ./data:/app/data
  - /var/run/docker.sock:/var/run/docker.sock:ro
```

This repository intentionally does **not** enable Docker socket access by default.

Mounting the Docker socket grants the container elevated access to the Docker daemon. Enable this feature only if Docker container monitoring is required and you fully understand the associated security implications. Avoid exposing an instance using Docker socket access directly to the public Internet.

## Troubleshooting

Check container status.

```bash
docker compose ps
```

View logs.

```bash
docker compose logs
```

View logs from Uptime Kuma only.

```bash
docker compose logs uptime-kuma
```

Restart the stack.

```bash
docker compose restart
```

## Tested With

- Uptime Kuma 2.5.0
- Embedded MariaDB
- Ubuntu 26.04 LTS
- Docker Engine 28.x
- Docker Compose v2

## License

This stack is distributed under the MIT License.

See the repository `LICENSE` file for details.
