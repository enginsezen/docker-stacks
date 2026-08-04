# Vaultwarden

Production-ready Docker Compose stack for Vaultwarden.

This stack is based on a real-world production deployment and has been adapted to follow the standards of this repository.

The goal is to provide a portable, reproducible, well-documented, and maintainable deployment that can be installed on a clean Docker host without relying on environment-specific configurations.

## Description

Vaultwarden is a lightweight, API-compatible implementation of the Bitwarden server written in Rust.

This stack provides a minimal production deployment using SQLite, persistent bind-mounted storage, and a version-pinned container image.

## Features

- Production-ready configuration
- SQLite database backend
- Persistent bind-mounted storage
- Secure by default
- Version-pinned container image
- Portable directory structure
- Repository-standard layout

## Architecture

```text
+----------------------+
|     Vaultwarden      |
|                      |
|  SQLite Database     |
|  Attachments         |
|  Configuration       |
+----------+-----------+
           |
      ./data/
```

## Requirements

Before deploying this stack, ensure the following requirements are met:

- Docker Engine 28.0 or newer
- Docker Compose v2

## Directory Structure

```text
vaultwarden/
├── docker-compose.yml
├── .env.example
├── .gitignore
└── README.md
```

After the first deployment, Docker will automatically create the required persistent directory.

Typical directory layout:

```text
vaultwarden/
└── data/
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

Verify that the container is running.

```bash
docker compose ps
```

## Environment Variables

| Variable | Required | Description |
|-----------|:--------:|-------------|
| `VAULTWARDEN_VERSION` | Yes | Vaultwarden image version |
| `TZ` | Yes | Time zone |
| `DOMAIN` | Yes | Public URL of the Vaultwarden instance |
| `ADMIN_TOKEN` | Yes | Argon2 PHC hash used to access the admin page |
| `SIGNUPS_ALLOWED` | Yes | Allow new user registrations |
| `WEBSOCKET_ENABLED` | Yes | Enable real-time notifications |

## Generating Secrets

Generate a secure Argon2 PHC hash for the admin page.

```bash
docker run --rm -it vaultwarden/server:${VAULTWARDEN_VERSION} /vaultwarden hash
```

Enter your desired admin password twice.

Copy the generated PHC string into the `ADMIN_TOKEN` variable inside `.env`.

Example:

```env
ADMIN_TOKEN='$argon2id$v=19$m=65540,t=3,p=4$...'
```

Use the original password (not the generated hash) to access the `/admin` page.

## Backup

Back up the following persistent directory regularly:

- `data/`

A repository-standard backup script will be added in a future revision.

## Restore

Restore the `data/` directory before starting the stack.

After restoring the data, start the service using:

```bash
docker compose up -d
```

A repository-standard restore script will be added in a future revision.

## Update

Pull the repository-supported container image.

```bash
docker compose pull
```

Recreate the container.

```bash
docker compose up -d
```

Verify that the service is running correctly.

```bash
docker compose ps
```

## Design Decisions

This stack intentionally follows the engineering standards defined for this repository.

Key design decisions include:

- SQLite is used as the official default database.
- Persistent data uses bind mounts.
- Image version is managed through `.env.example`.
- `ADMIN_TOKEN` uses an Argon2 PHC hash.
- No `container_name` is defined.
- No external Docker networks are required.
- No ports are published.
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

View Vaultwarden logs only.

```bash
docker compose logs vaultwarden
```

Restart the stack.

```bash
docker compose restart
```

## Tested With

- Docker Engine 28
- Docker Compose v2

## License

This stack is distributed under the MIT License.

See the repository `LICENSE` file for details.
