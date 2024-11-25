# Autopsy Multi-User Deployment Using Docker

This repository provides a `docker-compose.yml` configuration for deploying an Autopsy multi-user environment. The deployment includes Zookeeper, Solr, ActiveMQ, PostgreSQL, and a Samba file share server.

## Prerequisites

1. **Docker and Docker Compose**  
   Ensure Docker and Docker Compose are installed on your system.  
   - [Install Docker](https://docs.docker.com/get-docker/)  
   - [Install Docker Compose](https://docs.docker.com/compose/install/)

2. **Environment Variables**  
   Create a `.env` file in the root of your project with the following content:

   ```env
   ZOOKEEPER_VERSION=3.5.7
   SOLR_VERSION=8.6.3
   ACTIVEMQ_VERSION=5.14.0-alpine
   POSTGRES_VERSION=9.5.3
   AUTOPSY_USERNAME=autopsy
   AUTOPSY_PASSWORD=Changeme123!
   ```

## Deployment Steps

### 1. Clone the Repository

```bash
git clone git@github.com:cyber1709/autopsy-multiuser-using-docker.git
cd autopsy-multiuser-using-docker
```

### 2. Build and Deploy Containers

Run the following command to build and start all services:

```bash
docker-compose up -d --build
```
### 3. Get bash of autopsy-solr container to load configurations

```bash
docker exec -it autopsy-solr bash
cd /opt/solr
bin/solr create_collection -c autopsy -d /tmp/SOLR_8.6.3_AutopsyService/solr-8.6.3/server/solr/configsets/AutopsyConfig/conf
```

### 4. Verify Services

Check the status of the containers:

```bash
docker ps
```

Each container should be running as defined in the `docker-compose.yml` file.

## Service Overview

### Zookeeper

- **Purpose**: Coordination service for distributed systems.
- **Ports**: `2181`
- **Metrics Port**: `7000`
- **Configuration**:
  - `ZOO_MY_ID: 1`
  - `ZOO_SERVERS: server.1=zookeeper:2888:3888;2181`

### Solr

- **Purpose**: Search platform used by Autopsy for indexing.
- **Ports**: `8983`, `9983`
- **Dependencies**: Zookeeper
- **Resources**: 4 CPUs, 4GB memory
- **Volume**: `solr-data`

### ActiveMQ

- **Purpose**: Message broker for communication between services.
- **Ports**: `61616`
- **Dependencies**: None
- **Volume**: `activemq-config`, `activemq-data`

### PostgreSQL

- **Purpose**: Database for Autopsy's multi-user setup.
- **Ports**: `5432`
- **Configuration**:
  - Database: `autopsy`
  - User: `${AUTOPSY_USERNAME}`
  - Password: `${AUTOPSY_PASSWORD}`
- **Volume**: `pg-data`

### Samba

- **Purpose**: File sharing service for Autopsy.
- **Ports**: 
  - `137/udp`, `138/udp` (share advertisement)
  - `139/tcp`, `1045/tcp` (file sharing)
- **Dependencies**: None
- **Volume**: `samba-storage`

## Persistent Volumes

The following Docker volumes are used for storing persistent data:

- `solr-data`
- `zk-data`
- `zk-logs`
- `pg-data`
- `activemq-config`
- `activemq-data`
- `samba-storage`

## Networks

The services use the following networks:

- **Default Network**: Automatically created by Docker Compose.
- **`autopsy-backend`**: Custom network for internal communication between services.

## Managing the Deployment

### Stopping Services

To stop and remove all containers:

```bash
docker-compose down
```

### Viewing Logs

To view logs for a specific container:

```bash
docker logs <container_name>
```

Replace `<container_name>` with the desired service name, such as `autopsy-solr`.

## Additional Information

For more details, refer to the official Autopsy documentation:  
[Autopsy Multi-User Documentation](http://sleuthkit.org/autopsy/docs/user-docs/4.18.0/)

---

### Contributions

Feel free to open an issue or submit a pull request if you encounter problems or have suggestions.

---

### License

This repository is licensed under [MIT License](LICENSE).
```
