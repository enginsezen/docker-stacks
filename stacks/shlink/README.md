# Shlink

Production-ready Docker Compose stack for self-hosting Shlink with PostgreSQL and the official Shlink Web Client.

## Description

This stack deploys:

- Shlink
- PostgreSQL
- Shlink Web Client

The stack is designed for production environments while remaining simple, reproducible and easy to maintain.

It intentionally avoids infrastructure-specific assumptions such as reverse proxies, published ports or external Docker networks.

## Features

- Official Shlink image
- Official PostgreSQL image
- Official Shlink Web Client
- PostgreSQL backend
- Bind mounts only
- Image versions managed from `.env`
- No named volumes
- No external Docker networks
- No reverse proxy included
- No published ports in the repository version
- Production-ready defaults
- Persistent database storage

## Architecture

```
                +----------------------+
                |  Shlink Web Client   |
                +----------+-----------+
                           |
                           |
                    REST API (Internal)
                           |
                           v
                +----------------------+
                |       Shlink         |
                +----------+-----------+
                           |
                           |
                      PostgreSQL
                           |
                           v
                +----------------------+
                |      PostgreSQL      |
                +----------------------+
```

## Requirements

- Docker Engine
- Docker Compose

## Directory Structure

```
shlink/
├── docker-compose.yml
├── .env.example
├── .gitignore
├── README.md
├── postgres/
└── shlink/
```

## Quick Start

Clone the repository and enter the stack directory.

```bash
cp .env.example .env
```

Generate the required secrets.

Start the stack.

```bash
docker compose up -d
```

During local testing only, you may temporarily publish the internal ports for Shlink and the Web Client.

Remove all published ports before committing changes back to the repository.

## Environment Variables

| Variable | Description |
|-----------|-------------|
| SHLINK_VERSION | Shlink image version |
| SHLINK_WEB_CLIENT_VERSION | Shlink Web Client image version |
| POSTGRES_VERSION | PostgreSQL image version |
| TIMEZONE | Container timezone |
| DEFAULT_DOMAIN | Public Shlink domain |
| IS_HTTPS_ENABLED | Enable HTTPS URLs |
| SHLINK_SERVER_NAME | Display name in the Web Client |
| SHLINK_SERVER_URL | Shlink base URL |
| SHLINK_API_KEY | Initial API key |
| POSTGRES_DB | PostgreSQL database |
| POSTGRES_USER | PostgreSQL user |
| POSTGRES_PASSWORD | PostgreSQL password |

## Generating Secrets

Generate a PostgreSQL password.

```bash
openssl rand -base64 32
```

Generate the initial Shlink API key.

```bash
openssl rand -hex 32
```

## Backup

Backup the PostgreSQL database.

```bash
docker compose exec postgres \
pg_dump -U "$POSTGRES_USER" "$POSTGRES_DB" > shlink.sql
```

## Restore

Restore a PostgreSQL backup.

```bash
cat shlink.sql | docker compose exec -T postgres \
psql -U "$POSTGRES_USER" "$POSTGRES_DB"
```

## Update

Pull the updated images.

```bash
docker compose pull
```

Recreate the containers.

```bash
docker compose up -d
```

Remove unused images.

```bash
docker image prune
```

## Design Decisions

- PostgreSQL is used as the database backend.
- Only official Docker images are used.
- Image versions are pinned through `.env`.
- No ports are published in the repository version.
- Reverse proxy configuration is intentionally excluded.
- External Docker networks are intentionally excluded.
- Bind mounts are preferred over named volumes.
- Only production-required configuration is included.
- GeoLite2 integration is intentionally omitted.
- Mercure integration is intentionally omitted.

## Troubleshooting

### Web Client cannot connect

Verify that:

- `DEFAULT_DOMAIN`
- `SHLINK_SERVER_URL`
- `IS_HTTPS_ENABLED`

match your deployment.

When testing on localhost, temporarily configure these values for HTTP and localhost.

### Database connection failed

Verify:

- PostgreSQL container is running.
- Database credentials are correct.
- PostgreSQL data directory is writable.

### Changes to Web Client are not visible

The Web Client stores configured servers in browser local storage.

Clear the browser storage or use a private browsing session after changing server configuration.

## Tested With

- Ubuntu Server 24.04 LTS
- Docker Engine
- Docker Compose
- Shlink v5.1.5
- PostgreSQL 17.6-alpine
- Shlink Web Client v4.8.0

## License

This stack is provided under the MIT License.

See the repository root `LICENSE` file for details.
