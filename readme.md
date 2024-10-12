# Project Deployment Repository

Welcome to the **Project Deployment Repository**! This repository streamlines the deployment of multiple interconnected applications and services using Docker Compose. Whether you're deploying the frontend, backend API, worker service, or essential infrastructure components like RabbitMQ, Redis, and PostgreSQL, this guide will help you get everything up and running with ease.

## Table of Contents

- [Project Overview](#project-overview)
- [Prerequisites](#prerequisites)
- [Repository Structure](#repository-structure)
- [Environment Configuration](#environment-configuration)
- [Scripts](#scripts)
  - [1. Launch All Projects and Services](#1-launch-all-projects-and-services)
  - [2. Launch Only Services](#2-launch-only-services)
- [Docker Compose Profiles](#docker-compose-profiles)
- [Usage](#usage)
- [Healthchecks and Dependencies](#healthchecks-and-dependencies)
- [Data Persistence](#data-persistence)
- [Troubleshooting](#troubleshooting)
- [Contributing](#contributing)
- [License](#license)

## Project Overview

This repository orchestrates the deployment of the following components:

### Applications

1. **Frontend**: Built with Next.js.
2. **API**: Developed using Express.js (Node.js).
3. **Worker**: Created with Hono and Bun.

### Services

1. **PostgreSQL (`db`)**: Database service.
2. **RabbitMQ (`mq`)**: Message broker.
3. **Redis (`redis`)**: In-memory data store.

All services and applications communicate over a dedicated Docker network, ensuring seamless interconnectivity.

## Prerequisites

Before getting started, ensure you have the following installed on your system:

- **Docker**: [Download and Install Docker](https://docs.docker.com/get-docker/)
- **Docker Compose**: Comes bundled with Docker Desktop. For Linux, you might need to install it separately.
- **Git**: [Download and Install Git](https://git-scm.com/downloads)

## Repository Structure

The frontend, api, and worker projects are located elsewhere, inside the [FightCentral](https://github.com/orgs/FightCentral/repositories) organization repositories, and need to be cloned beforehand.  

The next paths are relative to the current repository :

- **../front/**: Contains the Next.js frontend application.
- **../api/**: Contains the Express.js API application.
- **../worker/**: Contains the Hono and Bun worker service.
- **docker-compose.yaml**: Docker Compose configuration defining all services and applications.
- **run_all.sh**: Script to launch all projects and services.
- **run_services.sh**: Script to launch only the essential services.
- **.env.example**: Example environment variables file.

## Environment Configuration

### 1. Create a `.env` File

Ensure you have a `.env` file in the root directory of your repository. This file holds sensitive information and environment-specific variables.

```bash
cp .env.example .env
```

### 2. Define Environment Variables

Edit the `.env` file to include your specific configuration:

```bash
# PostgreSQL Configuration
POSTGRES_USER=your_username
POSTGRES_PASSWORD=your_password
POSTGRES_DB=your_database

# RabbitMQ Configuration (optional)
# Add any RabbitMQ-specific environment variables if needed

# Redis Configuration (optional)
# Add any Redis-specific environment variables if needed

# API Configuration (if applicable)
# Add any API-specific environment variables if needed
```

## Scripts

To simplify the deployment process, two shell scripts have been provided:

- `run_all.sh`
- `run_services.sh`

### 1. Launch All Projects and Services

#### Script: `run_all.sh`

Purpose: Launches all applications (frontend, API, worker) along with all essential services (PostgreSQL, RabbitMQ, Redis).

Script Content:
```bash
#!/bin/bash
docker-compose --profile apps --profile services up -d
```

Explanation:

- `--profile apps`: Activates the apps profile, launching frontend, API, and worker.
- `--profile services`: Activates the services profile, launching PostgreSQL, RabbitMQ, and Redis.
- `up -d`: Starts the containers in detached mode.

### 2. Launch Only Services

#### Script: `run_services.sh`

Purpose: Launches only the essential services (PostgreSQL, RabbitMQ, Redis) without starting the frontend, API, or worker applications.

Script Content:
```bash
#!/bin/bash
docker-compose --profile services up -d
```

Explanation:

- `--profile services`: Activates only the services profile.
- `up -d`: Starts the containers in detached mode.

#### Make Scripts Executable

Before using the scripts, ensure they have execute permissions:

```bash
chmod +x run_all.sh run_services.sh
```

## Docker Compose Profiles

The `docker-compose.yaml` file uses profiles to group services. This allows for flexible deployment options.

## Usage

### 1. Launch All Projects and Services

To start everything (applications and services) with a single command:

```bash
./run_all.sh
```

What Happens:

- Docker Compose activates both `apps` and `services` profiles.
- Builds and starts the frontend, api, worker, db, mq, and redis services.
- All containers run in detached mode.

### 2. Launch Only Services

To start only the essential services without the applications:

```bash
./run_services.sh
```

What Happens:

- Docker Compose activates only the `services` profile.
- Builds and starts the db, mq, and redis services.
- Applications (frontend, api, worker) are not started.

## Healthchecks and Dependencies

### Healthchecks

Each service includes a healthcheck to ensure it is ready before dependent services attempt to connect:

- PostgreSQL (db): Uses `pg_isready` to check readiness.
- RabbitMQ (mq): Uses `rabbitmqctl status` to verify service status.
- Redis (redis): Uses `redis-cli ping` to confirm responsiveness.

### Dependencies

- Frontend: Depends on the api service.
- API: Depends on db and mq.
- Worker: Depends on mq.

Note: The `depends_on` directive ensures that Docker starts the dependent services first, but it does not wait for them to be "ready." Healthchecks help in managing service readiness.

## Data Persistence

### PostgreSQL Data

- Volume: `db-data`
- Path: `/var/lib/postgresql/data` inside the container.

This setup ensures that your PostgreSQL data persists across container restarts and recreations.

## Troubleshooting

1. **Container Fails to Start**:
   - Check logs using `docker-compose logs <service_name>`.
   - Ensure environment variables in `.env` are correctly set.

2. **Port Conflicts**:
   - Verify that the ports (3000, 8080, 15672, 5672) are not in use by other applications.

3. **Service Not Ready**:
   - Use `docker-compose ps` to check the status of services.
   - Ensure healthchecks are passing.

4. **Network Issues**:
   - Ensure all services are connected to the `app-network`.
   - Restart Docker if network issues persist.

## Contributing

Contributions are welcome! Please follow these steps:

1. Fork the Repository
2. Create a Feature Branch
   ```bash
   git checkout -b feature/my-feature
   ```
3. Commit Your Changes
   ```bash
   git commit -m "Add Your Feature"
   ```
4. Push to the Branch
   ```bash
   git push origin feature/my-feature
   ```
5. Open a Pull Request

## License

This project is licensed under the MIT License.

## Acknowledgements

- [Docker Documentation](https://docs.docker.com/)
- [Docker Compose Profiles](https://docs.docker.com/compose/profiles/)
- [Next.js](https://nextjs.org/)
- [Express.js](https://expressjs.com/)
- [Hono](https://hono.dev/)
- [Bun](https://bun.sh/)

Feel free to reach out if you have any questions or need further assistance. Happy deploying!
