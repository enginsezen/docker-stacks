# Docker Stacks

Production-ready Docker Compose stacks built from real-world usage.

This repository contains Docker Compose stacks that I personally use, test, maintain, and trust in production environments.

Every stack is built from real deployments, cleaned of environment-specific configuration, documented, and validated before publication.

The goal is not to collect Docker Compose examples.

The goal is to publish Docker Compose stacks that can be deployed again with confidence.

## Philosophy

This repository follows a few simple principles.

- Production first
- Quality over quantity
- Reproducible deployments
- Security by default
- Long-term maintainability
- Clear documentation

Every published stack represents infrastructure that has already proven itself in production.

## Repository Structure

```text
docker-stacks/
├── docs/
│   └── STANDARDS.md
├── stacks/
│   ├── n8n/
│   └── paperless-ngx/
├── .editorconfig
├── .gitattributes
├── .gitignore
├── LICENSE
└── README.md
```

Repository-wide engineering standards are documented in:

```text
docs/STANDARDS.md
```

## Available Stacks

| Stack | Description | Status |
|--------|-------------|--------|
| Paperless-ngx | Document management system using PostgreSQL and Redis | ✅ Production |
| n8n | Workflow automation platform using PostgreSQL, Redis and Queue Mode | ✅ Production |

More production-ready stacks will be added over time.

## Repository Standards

Every stack published in this repository:

- Is actively used in production
- Is validated before publication
- Includes documentation
- Provides a `.env.example`
- Uses pinned container image versions
- Avoids environment-specific configuration
- Can be deployed on a clean Docker host
- Follows the engineering standards defined in `docs/STANDARDS.md`

## Design Goals

Every stack should be:

- Easy to understand
- Easy to deploy
- Easy to maintain
- Easy to reproduce

Configuration should be explicit.

Documentation should explain both **how** and **why**.

## Contributing

Contributions are welcome.

If you discover an issue, have an improvement, or would like to contribute to an existing stack, feel free to open an issue or submit a pull request.

Please keep all contributions consistent with the repository standards.

## License

This project is licensed under the MIT License.

See the `LICENSE` file for details.
