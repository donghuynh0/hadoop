# Hadoop Docker Compose Startup Guide

## Prerequisites

- Hadoop version: **3.2.1**
- Java version: **8**
- Ensure you have cloned the repo:
  
  ```sh
  git pull https://github.com/donghuynh0/hadoop
  ```
- Docker and Docker Compose installed

## Components

### Master
- **Namenode**: HDFS metadata
- **Resourcemanager**: Manages cluster resources and schedules jobs across NodeManagers

### Worker
- **Datanode**: Stores actual HDFS data blocks
- **Nodemanager**: Manages individual node resources

### Support
- **Historyserver**: Provides access to job history logs for completed MapReduce jobs

---

## How to Run

### 1. Start Namenode (Master)

```sh
docker compose -f docker-compose.namenode.yml up -d
```

Check status:

## Hadoop Docker Compose — Quick start

This repository contains Docker Compose files to run a small Hadoop 3.2.1 cluster (NameNode, DataNode, ResourceManager, NodeManager and HistoryServer) for development and testing.

What you'll find here:
- Per-service Docker Compose files:
  - `docker-compose.namenode.yml`
  - `docker-compose.datanode.yml`
  - `docker-compose.resourcemanager.yml`
  - `docker-compose.nodemanager.yml`
  - `docker-compose.historyserver.yml`
- An optional aggregate `docker-compose.yml` (if present) to bring services up together.

## Before you begin

- Docker Engine (desktop or Linux) installed and running
- Docker Compose support. Use either the Compose V2 CLI (`docker compose`) or legacy `docker-compose` if you prefer. Examples below use `docker compose` (v2).
- Recommended Hadoop image in these compose files targets Hadoop 3.2.1. The containers include a JDK so you don't need Java installed on the host.
- Repository already cloned locally. If not, clone it:

```sh
git clone https://github.com/donghuynh0/hadoop.git
cd hadoop
```

There is an environment file `hadoop.env` in the repo — review it if you need to change exposed ports or any service environment variables.

## Quick start — bring up the full cluster

Start each service in order (NameNode and ResourceManager first, then workers, then HistoryServer):

```sh
docker compose -f docker-compose.namenode.yml up -d
docker compose -f docker-compose.resourcemanager.yml up -d
docker compose -f docker-compose.datanode.yml up -d
docker compose -f docker-compose.nodemanager.yml up -d
docker compose -f docker-compose.historyserver.yml up -d
```

Or, if this repo provides a combined `docker-compose.yml` that defines all services, you can bring everything up at once:

```sh
docker compose up -d
```

Check running containers:

```sh
docker ps --format "table {{.Names}}	{{.Status}}	{{.Ports}}"
```

Common container names to look for: `namenode`, `datanode`, `resourcemanager`, `nodemanager`, `historyserver`.

## Web UIs

- NameNode UI: http://<host>:9870
- ResourceManager UI: http://<host>:8088
- HistoryServer UI: http://<host>:8188

If you run Docker on the same machine as your browser, use `localhost` for `<host>`. If using a remote Docker host, use that host's IP or DNS name. If your containers are on a bridged Docker network, you can also open the NameNode UI by finding the mapped port on the host via `docker ps`.

## Useful commands

- View container logs (follow):
  ```sh
  docker logs -f <container_name>
  ```
- Exec into a running container (example for NameNode):
  ```sh
  docker exec -it namenode bash
  ```
- Stop a single service:
  ```sh
  docker compose -f docker-compose.namenode.yml down
  ```
- Stop all services (run the down command for each compose file or use `docker compose down` if you started with a combined compose file):
  ```sh
  docker compose -f docker-compose.namenode.yml down
  docker compose -f docker-compose.resourcemanager.yml down
  docker compose -f docker-compose.datanode.yml down
  docker compose -f docker-compose.nodemanager.yml down
  docker compose -f docker-compose.historyserver.yml down
  ```

## Troubleshooting

- Containers crash on start:
  - Check logs: `docker logs <container_name>`
  - Look for permission errors on mounted volumes — ensure the host directory has the correct owner and permissions for Docker to write.
- UI is unreachable:
  - Confirm the container is running: `docker ps`
  - Confirm the port is published on the host (see `PORTS` in `docker ps`). If not published, use `docker exec` to inspect the container network or access the UI from within the Docker host.
- Services can’t talk to each other:
  - Ensure services are in the same Docker network. The compose files in this repo are configured to put related services on a common network — if you modified them, double-check `networks` sections.

## Tips

- Start masters first (NameNode, ResourceManager), then workers (DataNode, NodeManager), then HistoryServer. This reduces startup race conditions.
- Use `docker compose logs -f` with a service name to follow logs from related containers.

## Example: automated startup script (optional)

Create a small script to start everything in order:

```sh
#!/usr/bin/env bash
set -euo pipefail

docker compose -f docker-compose.namenode.yml up -d
sleep 3
docker compose -f docker-compose.resourcemanager.yml up -d
sleep 2
docker compose -f docker-compose.datanode.yml up -d
docker compose -f docker-compose.nodemanager.yml up -d
sleep 1
docker compose -f docker-compose.historyserver.yml up -d

echo "All services started. Check UIs on ports 9870 (NameNode), 8088 (ResourceManager), 8188 (HistoryServer)."
```

Save it as `start-cluster.sh` and make it executable with `chmod +x start-cluster.sh`.

## Where to look next

- Inspect `hadoop.env` to change environment variables and port mappings.
- Open the compose files in this repo to see container images, volume mounts and exposed ports.

---

If you'd like, I can also:
- Add a `Makefile` or `start-cluster.sh` to automate the commands shown above.
- Add health-check commands or a combined `docker-compose.yml` that references the per-service files.

Requirements coverage:
- Clear startup commands: Done
- Per-service UIs and ports: Done
- Troubleshooting tips: Done

