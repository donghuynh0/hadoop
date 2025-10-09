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

```sh
docker ps -a
```

Look for `namenode` container running.

Access web UI:

- Open browser: `http://<namenode_ip>:9870`

---

### 2. Start ResourceManager (Master)

```sh
docker compose -f docker-compose.resourcemanager.yml up -d
```

Check status:

```sh
docker ps -a
```

Look for `resourcemanager` container running.

Access web UI:

- Open browser: `http://<resourcemanager_ip>:8088`

---

### 3. Start DataNode (Worker)

```sh
docker compose -f docker-compose.datanode.yml up -d
```

Check status:

```sh
docker ps -a
```

Look for `datanode` container running.

No separate web UI for datanode, but data node status can be checked in Namenode UI.

---

### 4. Start NodeManager (Worker)

```sh
docker compose -f docker-compose.nodemanager.yml up -d
```

Check status:

```sh
docker ps -a
```

Look for `nodemanager` container running.

NodeManager status can also be viewed in ResourceManager UI.

---

### 5. Start HistoryServer (Support)

```sh
docker compose -f docker-compose.historyserver.yml up -d
```

Check status:

```sh
docker ps -a
```

Look for `historyserver` container running.

Access web UI:

- Open browser: `http://<historyserver_ip>:8188`

---

## Useful Notes

- You can start all services in sequence to build a full Hadoop cluster.
- Replace `<namenode_ip>`, `<resourcemanager_ip>`, `<historyserver_ip>` with your Docker host IP or container IP as appropriate.
- For troubleshooting, check container logs:
  ```sh
  docker logs <container_name>
  ```

---

## Example Startup Sequence

```sh
docker compose -f docker-compose.namenode.yml up -d
docker compose -f docker-compose.resourcemanager.yml up -d
docker compose -f docker-compose.datanode.yml up -d
docker compose -f docker-compose.nodemanager.yml up -d
docker compose -f docker-compose.historyserver.yml up -d
```

---

## Stop All Services

```sh
docker compose -f docker-compose.namenode.yml down
docker compose -f docker-compose.resourcemanager.yml down
docker compose -f docker-compose.datanode.yml down
docker compose -f docker-compose.nodemanager.yml down
docker compose -f docker-compose.historyserver.yml down
```
