# trial-postgresql

Trial or micro-service unit of [PostgreSQL](https://www.postgresql.org/docs/) with [Adminer](https://www.adminer.org/) as Web UI.

For MySQL/MariaDB bootstrapper, checkout the [trial-mysql](https://github.com/kamaslau/trial-mysql).

## Service URL

- Database (PostgreSQL) [http://localhost:5432](http://localhost:5432)
- Web UI (Adminer) [http://localhost:8081](http://localhost:8081)

Ports could be changed with .env file, which is instructed underneath.

## Default user

Default logins should only be used in local/dev environments.

| Username | Password |
| -------- | -------- |
| root     | 123456   |

NOTE: Be sure to pick PostgreSQL for System in Adminer Login form.

## Usage

### Start with [Docker Compose](https://docs.docker.com/compose/)

```bash
# Initiate .env file, Docker Compose will automaticly pick it up
cp .env.example .env
# Start services
docker compose up -d
```

Update existing composed containers with latest images:

```bash
docker compose pull && \
docker compose down && \
docker compose up -d
```

### Further operations

```bash
# Enter container and initiate shell
docker exec -it postgres bash
```

## Relevent Docker images

- [Adminer](https://hub.docker.com/_/adminer)
- [PostgreSQL](https://hub.docker.com/_/postgres)
