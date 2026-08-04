# Repository Standards

This document defines the engineering standards followed by every stack in this repository.

The goal is to build a collection of production-ready Docker Compose stacks that are actively used, well documented, reproducible, and easy to maintain.

If a stack does not meet these standards, it does not belong in this repository.

---

# Philosophy

This repository is not a collection of Docker Compose examples.

Every stack included here is:

- Used in production
- Personally maintained
- Documented from real-world experience
- Designed to be reproducible
- Reviewed before publication

The repository reflects production deployments, not experiments.

---

# Engineering Principles

Every stack must follow these principles.

## Production First

A stack should first prove itself in production before being published.

Experimental or untested deployments are not included.

---

## Documentation First

Documentation is considered part of the project.

If something cannot be installed by following the README, the documentation is incomplete.

---

## Reproducibility

Every stack should be deployable using only the provided documentation.

A typical installation should look like:

```bash
cp .env.example .env
docker compose up -d
```

No undocumented manual steps should be required.

---

## Portability

Stacks must not depend on the author's environment.

Avoid assumptions such as:

- /opt/docker
- Custom directory layouts
- Existing Docker networks
- Existing reverse proxies

The repository should work on any clean Docker host.

---

## Security by Default

Security always takes priority over convenience.

- Never commit secrets.
- Never commit passwords.
- Never commit API keys.
- Never commit private certificates.

Use environment variables instead.

---

## Maintainability

A stack should remain understandable months or years after it was created.

Prefer clarity over cleverness.

---

# Docker Compose Standards

## Compose Specification

Do not use the deprecated version field.

Use the current Docker Compose Specification.

---

## Image Tags

Never use:

```yaml
image: redis:latest
```

Prefer pinned versions.

Example:

```yaml
image: redis:8.2.1
```

This ensures reproducible deployments.

---

## container_name

Avoid using container_name unless there is a strong technical reason.

Allow Docker Compose to manage container naming.

---

## Environment Variables

Configuration belongs in `.env.example`.

Secrets must never appear inside `docker-compose.yml`.

Good:

```yaml
POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}
```

Bad:

```yaml
POSTGRES_PASSWORD: mypassword
```

---

## Volumes

Prefer bind mounts whenever practical.

Example:

```text
./data
./config
./media
```

Absolute host paths should not be used inside the repository.

---

## Networks

Stacks should work without requiring external Docker networks.

Integration with Traefik, Caddy, or Nginx Proxy Manager should be documented as an optional step.

---

## Restart Policy

Use:

```yaml
restart: unless-stopped
```

unless another policy is explicitly required.

---

# Environment File Standards

Every stack must provide a `.env.example`.

It should contain:

- Required variables
- Safe default values
- Placeholder secrets

Example:

```env
TZ=Europe/Istanbul

POSTGRES_PASSWORD=change-me
SECRET_KEY=change-me
```

## Synchronization

The `.env.example` file and `docker-compose.yml` must always stay synchronized.

- Every variable referenced in `docker-compose.yml` must exist in `.env.example`.
- `.env.example` must not contain unused variables.
- Only user-configurable values should be exposed through `.env.example`.
- Configuration values intentionally fixed by the maintainer should remain in `docker-compose.yml`.

A variable should never be added to `.env.example` unless it is actually used by the stack.

---

# Secret Generation

README files should explain how to generate secure secrets.

Recommended examples:

```bash
openssl rand -base64 32
```

```bash
openssl rand -hex 64
```

Do not ask users to invent passwords manually.

---

# Documentation Standards

Every stack should contain a README with at least the following sections.

- Overview
- Features
- Requirements
- Directory Structure
- Environment Variables
- Deployment
- Backup
- Restore
- Update
- Design Decisions
- Troubleshooting
- License

The README should explain both **how** and **why**.

---

# Backup Policy

Backup scripts should be included only when they provide real value.

Stateless services usually do not require backup scripts.

Stateful services should provide documented backup procedures.

---

# Update Policy

Every stack must include documented update instructions.

If an update script exists, it should be documented.

---

# Validation

Every stack must be validated before publication.

Validation means deploying the stack on a clean Ubuntu 24.04 LTS installation by following only the provided README.

If additional undocumented steps are required, the documentation must be updated before publication.

---

# Stack Lifecycle

Each stack should progress through the following stages.

Production Deployment

↓

Repository Adaptation

↓

Documentation

↓

Validation

↓

Publication

The repository always reflects production-ready deployments.

---

# Quality Checklist

A stack is ready to publish only if all of the following are true.

- [ ] Used in production
- [ ] Production tested
- [ ] README completed
- [ ] Installation tested
- [ ] .env.example provided
- [ ] Secrets removed
- [ ] No latest image tags
- [ ] Design decisions documented
- [ ] Backup documented
- [ ] Restore documented
- [ ] Update documented
- [ ] Successfully installed on a clean Ubuntu 24.04 LTS system

---

# Repository Goal

The purpose of this repository is not to collect Docker Compose files.

The goal is to publish Docker Compose stacks that are:

- Used
- Tested
- Maintained
- Reproducible
- Properly documented

Every published stack should be something the maintainer would confidently deploy again from scratch.
