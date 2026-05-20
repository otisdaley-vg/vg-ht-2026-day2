# Hackathon Day Two

A dev container setup with a .NET app service and a Postgres database seeded with the Netflix movies dataset.

## What's inside

- **app** — prebuilt image `ghcr.io/otisdaley-vg/hackathon-daytwo:latest`, workspace mounted at `/workspaces/daytwo`
- **db** — Postgres 16, seeded from `.devcontainer/db/init/netflixdb-postgres.sql`
  - DB: `movies`, user/password: `hackathon` / `hackathon`
  - Exposed on host port `5432`
  - Connection string: `postgres://hackathon:hackathon@db:5432/movies`

## Prerequisites

- [Docker](https://docs.docker.com/get-docker/) running locally
- One of:
  - [VS Code](https://code.visualstudio.com/) + the [Dev Containers extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers)
  - [`@devcontainers/cli`](https://github.com/devcontainers/cli) (`npm install -g @devcontainers/cli`)

## Option 1: VS Code

1. Open the repo in VS Code: `code .`
2. When prompted, choose **Reopen in Container**. (Or run **Dev Containers: Reopen in Container** from the command palette.)
3. VS Code builds/pulls the images, starts both services, and attaches to the `app` container at `/workspaces/daytwo`.
4. Open a terminal inside the container — you're in the workspace.

To rebuild from scratch: command palette → **Dev Containers: Rebuild Container**.

## Option 2: devcontainer CLI

From the repo root:

```bash
# Bring the dev container up (pulls images, starts db + app, runs lifecycle hooks)
devcontainer up --workspace-folder .

# Open a shell inside the app container
devcontainer exec --workspace-folder . bash
```

Other useful commands:

```bash
# Run an arbitrary command inside the container
devcontainer exec --workspace-folder . dotnet --info

# Rebuild without using cache
devcontainer up --workspace-folder . --remove-existing-container

# Tear down
docker compose -f .devcontainer/docker-compose.yml down
# add -v to also drop the postgres-data volume (wipes the DB)
```

## Connecting to the database

From inside the container:

```bash
psql "$DATABASE_URL"
```

From the host (Postgres is published on `localhost:5432`):

```bash
psql postgres://hackathon:hackathon@localhost:5432/movies
```

## Optional: register the DB as an MCP server for Claude

`.scripts/mcp.txt` contains commands to wire the Postgres instance up as an MCP server via `@bytebase/dbhub`:

```bash
claude mcp add moviedb -- npx -y @bytebase/dbhub --transport stdio --dsn "postgres://hackathon:hackathon@db:5432/movies"
```
