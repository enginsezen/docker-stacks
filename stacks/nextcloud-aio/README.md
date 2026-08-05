# Nextcloud AIO

Production-ready Docker Compose stack for **Nextcloud All-in-One (AIO)**.

This stack follows the engineering standards of this repository while intentionally preserving the official Nextcloud AIO architecture where required.

Unlike a traditional multi-container Docker Compose deployment, Nextcloud AIO uses a **master container** that automatically provisions, configures, updates and manages all required service containers.

The objective of this repository is not to redesign the official deployment model, but to provide a clean, reproducible and production-ready implementation of it.

# Features

- Production-ready configuration
- Official Nextcloud AIO architecture
- Reverse proxy ready
- Automatic service orchestration
- Automatic application updates
- Built-in backup support
- Docker managed persistent storage
- Minimal configuration
- Secure by default

# Architecture

```text
                     +-------------------------------+
                     | Nextcloud AIO Mastercontainer |
                     +---------------+---------------+
                                     |
                              Docker Socket API
                                     |
        +----------------------------+----------------------------+
        |             |             |             |              |
    Nextcloud     PostgreSQL      Redis        Apache     Optional Services
                                                    |
                                               Reverse Proxy
                                                    |
                                                 Internet
```

Only the master container is defined in this repository.

All application containers are automatically created and managed by Nextcloud AIO.

# Requirements

Before deploying this stack, ensure the following requirements are met:

- Docker Engine 28.0 or newer
- Docker Compose v2

# Directory Structure

```text
nextcloud-aio/
├── docker-compose.yml
├── .env.example
├── .gitignore
└── README.md
```

Docker automatically creates the required named volumes during the first deployment.

# Quick Start

Copy the example environment file.

```bash
cp .env.example .env
```

Validate the compose configuration.

```bash
docker compose config
```

Start the stack.

```bash
docker compose up -d
```

Verify the container status.

```bash
docker compose ps
```

For the initial installation you may temporarily publish the AIO interface port (container port `8080`) to a host port such as `8195`.

Temporary port publishing is only required for the initial setup and local testing and is intentionally omitted from the repository version.

# Environment Variables

| Variable | Required | Description |
|-----------|:--------:|-------------|
| `NEXTCLOUD_AIO_CHANNEL` | Yes | Official AIO release channel (`latest` or `beta`) |
| `NEXTCLOUD_DATADIR` | Yes | Docker volume used for persistent Nextcloud data |

# Generating Secrets

No manual secret generation is required.

The initial AIO passphrase is automatically generated during the first startup.

# Backup

Back up the following Docker volumes regularly:

- `nextcloud_aio_mastercontainer`
- `nextcloud_aio_nextcloud_datadir`

Also back up the following files if you modified them:

- `.env`
- `docker-compose.yml`

The master container volume stores the AIO configuration and is required by the built-in backup mechanism.

The Nextcloud data volume stores all persistent user data.

# Restore

Restore both Docker volumes before starting the stack.

Start the services.

```bash
docker compose up -d
```

If you use the built-in AIO backup feature, follow the official restore procedure.

# Update

Pull the newest master container.

```bash
docker compose pull
```

Restart the stack.

```bash
docker compose up -d
```

The master container automatically manages updates for all AIO-managed containers.

# Design Decisions

This repository follows strict engineering standards.

Nextcloud AIO is intentionally treated as a special case because several implementation details are mandatory according to the upstream project.

## Official container name

Repository standard:

- `container_name` is not used.

Exception:

The master container **must** be named:

```text
nextcloud-aio-mastercontainer
```

The upstream project explicitly states that changing the container name is unsupported because the AIO update mechanism depends on it.

Therefore, this repository intentionally preserves the official container name.

## Official master volume

Repository standard:

- Bind mounts are preferred.

Exception:

The Docker volume

```text
nextcloud_aio_mastercontainer
```

must retain its official name.

The built-in backup and restore mechanism depends on this exact Docker volume.

Replacing it with a bind mount is intentionally avoided in order to preserve compatibility with the official AIO architecture.

## Official release channel

Repository standard:

- Container images are version pinned.

Exception:

Nextcloud AIO is distributed using official release channels.

The master container is responsible for orchestrating updates for every managed service.

For this reason, this repository intentionally follows the official release channel instead of pinning a specific image version.

Default:

```text
latest
```

Advanced users may switch to:

```text
beta
```

to test upcoming releases.

## Docker managed persistent data

Unlike the other stacks in this repository, Nextcloud user data is stored in the official Docker volume

```text
nextcloud_aio_nextcloud_datadir
```

instead of a bind mount.

This keeps the stack portable, avoids making assumptions about the user's filesystem layout and remains fully compatible with the official Nextcloud AIO deployment model.

Users who prefer storing the data on a host directory may configure `NEXTCLOUD_DATADIR` according to the official documentation.

## Reverse Proxy

This repository is designed to work behind an external reverse proxy.

Reverse proxy configuration is intentionally not included because networking differs between environments.

Configure your preferred reverse proxy according to the official Nextcloud AIO documentation.

Temporary port publishing may be used during the initial installation and local testing but is intentionally removed before committing the stack.

# Troubleshooting

Validate the compose configuration.

```bash
docker compose config
```

Show running containers.

```bash
docker compose ps
```

Show logs.

```bash
docker compose logs
```

Show master container logs.

```bash
docker compose logs nextcloud-aio-mastercontainer
```

Restart the stack.

```bash
docker compose restart
```

## Forgot the AIO passphrase

As long as the master container volume still exists, the generated passphrase can be retrieved from the configuration.

```bash
docker exec nextcloud-aio-mastercontainer \
cat /mnt/docker-aio-config/data/configuration.json
```

# Tested With

Local validation completed:

- Ubuntu Linux 26.04 LTS
- Docker Engine 28.x
- Docker Compose v2
- `docker compose config`
- Master container startup
- Docker volume creation
- Initial AIO interface
- Persistent master configuration

Production validation behind a reverse proxy should be completed before deploying this stack in production.

# License

This stack is distributed under the MIT License.

See the repository `LICENSE` file for details.
