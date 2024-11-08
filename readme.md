# Hadoop Distributed File System (HDFS) Cluster Setup
[![Docker](https://img.shields.io/badge/Docker-Required-blue.svg)](https://www.docker.com/)
[![Hadoop](https://img.shields.io/badge/Hadoop-3.2.1-yellow.svg)](https://hadoop.apache.org/)

This repository provides a simple and efficient way to set up a distributed Hadoop HDFS (Hadoop Distributed File System) cluster using Docker containers. The setup includes one NameNode (the master) and two DataNodes (the workers) to simulate a basic HDFS environment for testing or learning purposes.

## Prerequisites

- Docker Engine (version 19.03.0+)
- Docker Compose (version 1.27.0+)
- At least 4GB of RAM available
- At least 10GB of free disk space

## Architecture Overview
The setup consists of three key components:

- 1 NameNode: Acts as the master node managing the filesystem's metadata, serving the HDFS Web UI on port 9870 and the NameNode - service on port 9000.
- 2 DataNodes: Two worker nodes responsible for storing data in the HDFS. Each DataNode has its own Web UI for monitoring.
- 3 Volumes: Persistent volumes for storing HDFS data, ensuring that data is preserved even if the containers are restarted.


### Port Configuration

- NameNode Web UI: 9870
- NameNode Service: 9000
- DataNode 1 Web UI: 9864
- DataNode 2 Web UI: 9865

## Quick Start

1. Clone this repository:
```bash
git clone https://github.com/L00kAhead/hadoop_cluster
cd hadoop_cluster
```

2. Start the cluster:
```bash
docker-compose up -d
```

3. Verify the cluster is running:
```bash
docker ps
```

4. Access the NameNode Web UI:
```
http://localhost:9870
```

## Configuration Files

### Docker Compose Configuration
The `docker-compose.yml` file defines the following services:

- **namenode**: The HDFS master node
- **datanode1**: First HDFS worker node
- **datanode2**: Second HDFS worker node

### Hadoop Configuration

#### core-site.xml
Basic HDFS configuration file specifying the NameNode address:
```xml
<configuration>
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://namenode:9000</value>
    </property>
</configuration>
```

#### Environment Variables (hadoop.env)
Contains essential Hadoop configuration parameters:
- HDFS service configuration
- Permission settings
- NameNode and DataNode directory paths
- Handler count settings

## Volume Management

The following persistent volumes are configured:
- `hadoop_namenode`: Stores NameNode metadata
- `hadoop_datanode1`: Stores data for first DataNode
- `hadoop_datanode2`: Stores data for second DataNode

## Basic Usage

### Check Cluster Status
```bash
# List running containers
docker ps

# Check NameNode logs
docker logs namenode

# Check DataNode logs
docker logs datanode1
docker logs datanode2
```

### Access HDFS
```bash
# Enter NameNode container
docker exec -it namenode bash

# Basic HDFS commands
hdfs dfs -ls /
hdfs dfs -mkdir /user
hdfs dfs -put localfile /user/
```

## Maintenance

### Scaling
To add more DataNodes:
1. Copy the datanode service configuration in docker-compose.yml
2. Update the container name and volume mapping
3. Add new volume definition
4. Restart the cluster

### Stopping the Cluster
```bash
docker-compose down  # Stops containers
docker-compose down -v  # Stops containers and removes volumes
```

## Troubleshooting

1. If DataNodes are not connecting:
   - Check if NameNode is properly running
   - Verify network connectivity
   - Check logs using `docker logs <container-name>`

2. If Web UI is not accessible:
   - Verify port mappings
   - Check if containers are running
   - Ensure no port conflicts

## Security Considerations

- Default configuration disables permissions (`HDFS_CONF_dfs_permissions_enabled=false`)
- Web HDFS is enabled by default
- Root user is configured as static user
