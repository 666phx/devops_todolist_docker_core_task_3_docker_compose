# Instruction: Running Django-Todolist with Docker Compose

This project is fully containerized: the Django application and the MySQL database each run in their own container, orchestrated by `docker-compose.yml`.

## Prerequisites

- [Docker](https://docs.docker.com/get-docker/) installed and running
- [Docker Compose](https://docs.docker.com/compose/install/) (bundled with recent Docker Desktop as the `docker compose` plugin)

## Services

| Service      | Description                       | Port (host:container) |
|--------------|------------------------------------|------------------------|
| `mysql`      | MySQL database, persists data via the `db-data` named volume | 3306:3306 |
| `django_app` | Django Todolist application (runs migrations, then `manage.py runserver`) | 8080:8080 |

## Starting the containers

From the project root (where `docker-compose.yml` is located), run:

```bash
docker-compose up --build
```

- `--build` forces Docker Compose to (re)build the images from `Dockerfile` and `Dockerfile.mysql` — use it the first time, or any time you change the Dockerfiles/source code.
- Compose will start `mysql` first and wait until it reports healthy (via its healthcheck) before starting `django_app`, which then runs database migrations automatically on startup and launches the server.

To run in the background (detached mode), add `-d`:

```bash
docker-compose up --build -d
```

Once running, the app is available at:

```
http://localhost:8080/
```

The API is available at:

```
http://localhost:8080/api/
```

## Viewing logs

```bash
docker-compose logs -f
```

Or for a single service:

```bash
docker-compose logs -f django_app
docker-compose logs -f mysql
```

## Stopping the containers

To stop the containers without removing them:

```bash
docker-compose stop
```

To stop and remove the containers (and the default network Compose created), while **keeping** the data volume intact:

```bash
docker-compose down
```

## Removing everything, including stored data

If you also want to wipe the MySQL data (the persistent volume `db-data`), run:

```bash
docker-compose down -v
```

The next `docker-compose up` after this will start with a completely fresh, empty database.
