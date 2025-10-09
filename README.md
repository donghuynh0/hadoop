# Hadoop on Docker — Quick Start

Run a small Hadoop 3.2.1 cluster locally with Docker Compose for development and testing.

## Prerequisites

- Docker Engine running (Desktop or Linux)
- Docker Compose (v2 `docker compose` or legacy `docker-compose`)
- Git repository cloned:

  ```sh
  git clone https://github.com/donghuynh0/hadoop.git
  cd hadoop
  ```

Note: The containers bundle a JDK; you do not need Java on the host.

## Services

- NameNode: HDFS metadata (master)
- DataNode: HDFS data blocks (worker)
- ResourceManager: Cluster resource scheduler (master)
- NodeManager: Node-level resource manager (worker)
- HistoryServer: MapReduce job history UI

## TL;DR — start everything

Start masters first, then workers, then HistoryServer:

```sh
docker compose -f docker-compose.namenode.yml up -d
docker compose -f docker-compose.resourcemanager.yml up -d
docker compose -f docker-compose.datanode.yml up -d
docker compose -f docker-compose.nodemanager.yml up -d
docker compose -f docker-compose.historyserver.yml up -d
```

If a combined `docker-compose.yml` exists, you can instead do:

```sh
docker compose up -d
```

Verify containers:

```sh
docker ps --format "table {{.Names}}	{{.Status}}	{{.Ports}}"
```

Common names: `namenode`, `datanode`, `resourcemanager`, `nodemanager`, `historyserver`.

## Web UIs

- NameNode: http://localhost:9870
- ResourceManager: http://localhost:8088
- HistoryServer: http://localhost:8188

If Docker runs on a remote host, replace `localhost` with that host's IP or DNS name. You can also confirm published ports with `docker ps`.

## Manage the cluster

- Follow logs:
  ```sh
  docker logs -f <container_name>
  ```
- Shell into a container (example NameNode):
  ```sh
  docker exec -it namenode bash
  ```
- Stop one service:
  ```sh
  docker compose -f docker-compose.<service>.yml down
  ```
- Stop everything (per-file):
  ```sh
  docker compose -f docker-compose.namenode.yml down
  docker compose -f docker-compose.resourcemanager.yml down
  docker compose -f docker-compose.datanode.yml down
  docker compose -f docker-compose.nodemanager.yml down
  docker compose -f docker-compose.historyserver.yml down
  ```
- Stop everything (combined file):
  ```sh
  docker compose down
  ```

## Configuration

Review `hadoop.env` to adjust ports or service environment variables. See the individual compose files for images, volumes and networks.

## Troubleshooting

- Containers crash at start
  - Check logs: `docker logs <container_name>`
  - Validate permissions on mounted host directories
- UI unreachable
  - Ensure container is running: `docker ps`
  - Confirm the port is published on the host (`PORTS` column)
- Services cannot reach each other
  - Ensure they are on the same Docker network; double-check `networks` in compose files

## Optional: start script

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

echo "All services started. UIs: 9870 (NameNode), 8088 (ResourceManager), 8188 (HistoryServer)"
```

Save as `start-cluster.sh` and `chmod +x start-cluster.sh`.

---

Want automation or health checks added (Makefile, combined compose, healthchecks)? Open an issue or PR.

