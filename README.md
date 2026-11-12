# infra-blocks-postgresql

[![Blocks](https://img.shields.io/badge/infra--blocks-index-555?style=flat-square)](https://github.com/kamaslau/infra-blocks)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT)
![Repository size](https://img.shields.io/github/repo-size/kamaslau/infra-blocks-postgresql?color=56BEB8)

A unit, not a demo: one service, one port, one documented credential, disposable with a single command.

Typical use: someone other than you needs to open the data and look at it, and that person is not going to install a database client to do it.

PostgreSQL with [Adminer](https://www.adminer.org/). For MySQL/MariaDB, see [infra-blocks-mysql](https://github.com/kamaslau/infra-blocks-mysql).

## Service URL

- Database (PostgreSQL) `localhost:5432`
- Web UI (Adminer) [http://localhost:8081](http://localhost:8081)

Ports can be changed in `.env`; see Usage.

## Default user

Defaults are for local use only.

| Username | Password |
| -------- | -------- |
| root     | 123456   |

In Adminer's login form, pick **PostgreSQL** as the system. The default is MySQL, and the failure it gives back is not the one you would expect.

## Usage

### Start with [Docker Compose](https://docs.docker.com/compose/)

```bash
# Initiate .env file, Docker Compose will pick it up automatically
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
# Shell inside the database container
docker exec -it postgres bash

# Or straight into psql
docker exec -it postgres psql -U root -d postgres
```

## Verify

```bash
docker compose exec -T postgres psql -U root -d postgres -tAc 'select version();'
```

One line comes back, starting `PostgreSQL 17`. `docker compose ps` should show the database as `(healthy)` — Adminer is held back until that healthcheck passes.

## Why this shape

Adminer is bundled even though it is a liability in production, because the failure this unit exists for is not intrusion — it is an operations person who needs to check one row and has no client installed. The mitigation is in the README and in the compose file: defaults are declared local-only, and only the two documented ports are published.

The database sits on a named volume mounted at `/var/lib/postgresql/data`, so `docker compose down` keeps the data and `down -v` is the only thing that removes it. Until 2026-11-13 this path read `/var/lib/postgres/data`, which the image never writes to: the volume was created on every run and stayed empty, and the data actually lived in the container layer.

`depends_on: condition: service_healthy` with a `pg_isready` check is there because Adminer starts in under a second and Postgres does not. Without it the first login fails, and it fails in a way that reads like a wrong password.

The 2G / 2-CPU limit is set because this unit usually runs beside something else on a 2C4G box, and an unbounded Postgres will take the box before it takes a query timeout.

## Notes for deployments in China

Both images need a registry mirror on most mainland hosts. Pull them before the machine is behind the client's firewall, or configure `registry-mirrors` in `/etc/docker/daemon.json` and restart Docker.

---

Part of [infrastructure blocks](https://github.com/kamaslau/infra-blocks) · ← back to index
