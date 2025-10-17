# Hadoop on Docker

## Services

- NameNode: HDFS metadata (master)
- DataNode: HDFS data blocks (worker)
- ResourceManager: Cluster resource scheduler (master)
- NodeManager: Node-level resource manager (worker)
- HistoryServer: MapReduce job history UI

## Prerequisites

- Docker 
- Git repository cloned:

  ```sh
  git clone https://github.com/donghuynh0/hadoop.git
  cd hadoop
  ```

Note: You do not need Java on the host.

## Configuration

Review `hadoop.env` and `compose` file to adjust `namenode` by actual namenode ip.

## Start

Start masters first, then workers, then HistoryServer:

```sh
docker compose -f docker-compose.namenode.yml up -d
docker compose -f docker-compose.resourcemanager.yml up -d
docker compose -f docker-compose.datanode.yml up -d
docker compose -f docker-compose.nodemanager.yml up -d
docker compose -f docker-compose.historyserver.yml up -d
```

run all once 

```sh
docker compose up -d

```

Verify containers:

```sh
docker ps --format "table {{.Names}} {{.Status}} {{.Ports}}"
```

## 🌐 Web UIs

- NameNode:
  ```sh
  http://<namenode_ip>:9870
  ```
- ResourceManager:
  ```sh
  http://<namenode_ip>:8088
  ``` 
- HistoryServer:
  ```sh
  http://<namenode_ip>:8188
  ``` 


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
