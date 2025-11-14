---
layout: documentation
title: "Docker Compose for Local Development"
slug: docker-compose-usage
short_description: "How to start, stop, rebuild, and debug your local project using an existing docker-compose file."
last_update: 2025-11-14
---

## Scope

This guide shows developers how to use an existing Docker Compose setup to run a project locally.

You will learn how to:

- Start and stop services
- Watch logs and debug containers
- Run one‑off commands (migrations, tests, shells)
- Rebuild when dependencies change
- Work with env files, overrides, volumes, and profiles
- Fix common issues quickly

All commands below use Docker Compose v2 syntax: `docker compose ...` (a space, not a hyphen). If your system still uses
v1, replace with `docker-compose ...`.

---

## Prerequisites

- Docker Desktop (Windows/macOS) or Docker Engine (Linux)
- Docker Compose v2 (bundled with modern Docker)

Verify installation:

```bash
docker --version
docker compose version
```

If `docker compose` fails, try `docker-compose version`. Install or upgrade Docker if needed.

---

## Where are the files?

Your repository typically contains one or more of these:

- `docker-compose.yml` or `compose.yaml` — main services definition
- `docker-compose.override.yml` — local overrides (auto‑loaded by Compose if present)
- `.env` — environment variables used by the compose files (not committed in many repos)

Compose automatically loads `docker-compose.yml` + `docker-compose.override.yml` if present. To use custom names or
multiple files, provide them in order (left to right = lower to higher priority):

```bash
# Example: base + local overrides
docker compose -f docker-compose.yml -f docker-compose.override.yml up -d
```

Show what Compose would do without running it:

```bash
docker compose config
```

---

## First run (typical)

From the repository root where the compose file is:

```bash
# 1) Pull prebuilt images (if services come from a registry)
docker compose pull

# 2) Build images (if your services have local Dockerfiles)
docker compose build

# 3) Start everything in the background (detached)
docker compose up -d

# 4) See what’s running
docker compose ps

# 5) Tail logs for all services
docker compose logs -f --tail=100
```

Stop all services but keep data (volumes):

```bash
docker compose stop
```

Completely shut down and remove containers, default network, and anonymous images (not volumes):

```bash
docker compose down
```

Also remove named volumes (DANGEROUS: deletes persistent data):

```bash
# Do this only if you intend to reset databases, caches, etc.
docker compose down -v
```

---

## Everyday commands

- Start everything (detached):

```bash
docker compose up -d
```

- Start only one service (and its dependencies):

```bash
docker compose up -d api
```

- Restart a service:

```bash
docker compose restart api
```

- Stop everything:

```bash
docker compose stop
```

- List containers of this project:

```bash
docker compose ps
```

- Follow logs (all services or one):

```bash
docker compose logs -f
docker compose logs -f api
```

- Shell into a container:

```bash
docker compose exec api sh
# or bash if available
docker compose exec api bash
```

- Run a one‑off command in a new container (does not need the service to be up):

```bash
docker compose run --rm api ./manage.py migrate
docker compose run --rm web npm test
```

---

## Rebuilds and updates

When code or dependencies change, rebuild images:

```bash
# Rebuild all
docker compose build

# Rebuild a single service
docker compose build web

# Rebuild ignoring cache (slower but fresh)
docker compose build --no-cache web
```

Start with rebuild in one step:

```bash
docker compose up -d --build
```

If images are pulled from a registry and you want the latest:

```bash
docker compose pull
```

---

## Environment variables and overrides

- `.env` file in the project root is automatically read by Compose and used for variable substitution like `${VAR_NAME}`
  in compose files.
- Local override file: `docker-compose.override.yml` adjusts ports, volumes, env, resources for dev. It is automatically
  used if present.
- To use a specific override file, list it explicitly with `-f` (order matters):

```bash
docker compose -f docker-compose.yml -f docker-compose.dev.yml up -d
```

- Per‑service environment can be defined via `environment:` or `env_file:` sections in compose files.
- Secrets: if your project uses Docker secrets, follow the repo’s instructions to mount or provide them securely. Never
  commit secrets.

Tip: Show the fully-resolved config (after env/overrides):

```bash
docker compose config
```

---

## Working with data (volumes and bind mounts)

- Named volumes persist data across `up`/`down`. Removing them (`down -v`) deletes data.
- Bind mounts mirror local folders into containers (great for live code reload). On Linux, file permissions may need
  adjustment so the container user can write.

Useful commands:

```bash
# List volumes
docker volume ls
# Inspect a volume
docker volume inspect <name>
# Remove an unused volume (careful)
docker volume rm <name>
```

Reset app data completely (dangerous):

```bash
docker compose down -v
docker volume prune # removes ALL unused volumes on your system
```

## Profiles (optional)

Some compose setups use profiles to include/exclude services:

```bash
# Start only services in the "dev" profile
docker compose --profile dev up -d
# Include multiple profiles
docker compose --profile dev --profile mail up -d
```

Check which profiles exist by inspecting the compose files or running `docker compose config`.

---

## Troubleshooting

- Port already in use (`bind: address already in use`):
    - Change the published port in your override file, or stop the process occupying it.
    - Find the process on a port 3000 (Linux/macOS): `lsof -i :3000` then `kill <pid>`.

- File permission errors on Linux with bind mounts:
    - Ensure the container runs as a user that can write to the mounted directory, or adjust ownership:

```bash
sudo chown -R $USER:$USER .
```

- Some stacks use `user: "$(id -u):$(id -g)"` in overrides for dev.

- Containers exit immediately:
    - Check logs: `docker compose logs --no-color <service>`
    - Try running the service command manually with `docker compose run --rm <service> <command>`

- Stale/incorrect dependencies:
    - Rebuild without cache: `docker compose build --no-cache`
    - Remove containers and start fresh: `docker compose down && docker compose up -d --build`
    - Reset data (only if acceptable): `docker compose down -v`

- Orphan containers warning:

```bash
docker compose up -d --remove-orphans
```

- Disk space issues (`no space left on device`):

```bash
docker system df
docker system prune -f # removes unused data (careful)
docker volume prune -f # removes unused volumes
```

- Networking between services:
    - Use service names as hostnames (e.g., app connects to `db:5432`).
    - From host to a container, use the published port (e.g., `localhost:8080`).
    - From container to host: `host.docker.internal` works on Docker Desktop; on Linux, add
      `--add-host=host.docker.internal:host-gateway` in compose or use the host IP.

---

## Quick cheat sheet

```bash
# Start/Stop
docker compose up -d
docker compose stop
docker compose down # remove containers/network
docker compose down -v # also remove volumes (data!)

# Status/Logs
docker compose ps
docker compose logs -f --tail=100

# Rebuild/Update
docker compose build [service]
docker compose build --no-cache [service]
docker compose up -d --build
docker compose pull

# Interact
docker compose exec <svc> sh|bash
docker compose run --rm <svc> <cmd>

# Compose files and env
docker compose config
MY_FLAG=1 docker compose up -d
```

---

## FAQ

- Do I need Docker Desktop on Windows?
    - Yes, recommended. Use WSL2 backend if possible. Run commands in a WSL shell for best performance.

- Where do I change ports or enable hot reload?
    - In your `docker-compose.override.yml` (or a `*-dev.yml`) that you use locally.

- My code changes aren’t visible in the container.
    - Ensure the service uses a bind mount to your source folder and the app’s dev server supports hot reload.
      Otherwise, rebuild: `docker compose up -d --build`.

- How do I reset everything to a clean state?
    - `docker compose down -v` (deletes containers, network, and volumes). Then `docker compose up -d --build`.
