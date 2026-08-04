
# Docker Stacks

Production-ready Docker Compose stacks built from real-world usage.

This repository contains Docker Compose stacks that I personally use, test, maintain, and trust. Every stack is designed to be simple, documented, reproducible, and suitable for long-term maintenance.

Unlike many Docker Compose collections, this repository is **not** intended to be a directory of copied examples. Every stack exists because it solves a real problem and has been validated through actual use.

## Philosophy

This project follows a few simple principles.

* **Quality over quantity**
* **Production-first**
* **Minimal by design**
* **Security by default**
* **Well documented**
* **Long-term maintainability**

If a service cannot meet these standards, it will not be included.

## Repository Structure

```text
docker-stacks/
├── README.md
├── LICENSE
├── .gitignore
└── stacks/
    └── service/
        ├── README.md
        ├── docker-compose.yml
        ├── .env.example
        ├── backup.sh
        ├── restore.sh
        └── update.sh
```

Each stack is self-contained and includes everything required to deploy and maintain the service.

## What Makes This Repository Different?

Every stack in this repository is:

* Personally tested
* Used in real environments
* Maintained over time
* Documented with design decisions
* Free of personal configuration
* Easy to reproduce

The goal is not to provide hundreds of Docker Compose files.

The goal is to provide a small collection of reliable stacks that people can actually trust.

## Available Stacks

No stacks have been published yet.

The first stack will be added after it has been fully documented and tested.

## Contributing

Contributions are welcome.

If you would like to improve an existing stack, fix an issue, or suggest enhancements, feel free to open an issue or submit a pull request.

Please keep changes consistent with the project's philosophy.

## License

This project is licensed under the MIT License.
