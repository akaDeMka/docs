# Docker

## Docker commands

```bash
#для определенной платформы
docker build --platform linux/amd64 -t YOUR-USER-NAME/getting-started --restart unless-stopped
#volume create and mount
docker volume create todo-db
docker run -dp 127.0.0.1:3000:3000 --mount type=volume,src=todo-db,target=/etc/todos getting-started
#чекнуть volume
docker volume inspect todo-db
#bind mount
docker run -it --mount type=bind,src="$(pwd)",target=/src ubuntu bash
# Set working directory. -w - working directory
docker run -dp 127.0.0.1:3000:3000 -w /app --mount type=bind,src="$(pwd)",target=/app node:18-alpine sh -c "yarn install && yarn run dev"
#show container logs. <nodemon> - monitor file change and restart app automatically after file change
docker logs -f <container-id>
#create network
docker network create todo-app
docker run -d --network todo-app --network-alias mysql -v todo-mysql-data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=secret -e MYSQL_DATABASE=todos mysql:8.0
docker exec -it <mysql-container-id> mysql -u root -p
#database variables fo app container
-e MYSQL_HOST=mysql \
-e MYSQL_USER=root \
-e MYSQL_PASSWORD=secret \
-e MYSQL_DB=todos \
#database env
MYSQL_ROOT_PASSWORD: secret
MYSQL_DATABASE: todos
#вывести логи контейнеров
docker compose logs -f
#Remove all except volumes
docker compose down
#Show all layers. --no-trunc - не сокращать записи
docker image history --no-trunc <getting-started>
#check docker networks
docker network ls
#collect metrics (CPU, memory use, etc)
docker stats
#Check container logs
docker logs
docker service logs
#Daemon logs
journalctl -xu docker.service   #or read /var/log/syslog or /var/log/)
#Push to registry
docker login <REGISTRY_HOST>:<REGISTRY_PORT>
docker tag <IMAGE_ID> <REGISTRY_HOST>:<REGISTRY_PORT>/<APPNAME>:<APPVERSION>
docker push <REGISTRY_HOST>:<REGISTRY_PORT>/<APPNAME>:<APPVERSION>
#Copy docker volume data thought container
docker run --rm -v $PWD:/source -v my_volume_name:/dest -w /source alpine cp my_file_name.txt /dest
```

### docker build

```bash
#Multi-stage builds
# syntax=docker/dockerfile:1
FROM maven AS build
WORKDIR /app
COPY . .
RUN mvn package
FROM tomcat
COPY --from=build /app/target/file.war /usr/local/tomcat/webapps 
```

## Docker Theory

### Docker best practise

1. Use an official and verified Docker image as a base image, whenever available.
2. Use specific Docker image versions:

   - you might get a different image version as in the previous build
   - the new image version may break stuff
   - latest tag is unpredictable, causing unexpected behavior

3. Use Small-Sized Official Images -> Image Size+Security Issue
4. Optimize caching for image layers when building an image - all layers after change will rebuild - so first unchangable commands in dockerfile
5. Use .dockerignore file
6. Make use of Multi-Stage Builds
7. Use the Least Privileged User - Security
8. Scan your Images for Security Vulnerabilities - docker scan

### Docker storages

- **Volumes** - created and managed by Docker. You can create a volume explicitly using the docker volume create command, or Docker can create a volume during container or service creation.
Bind mounts
- **tmpfs mounts** - A tmpfs mount is not persisted on disk, either on the Docker host or within a container. It can be used by a container during the lifetime of the container, to store non-persistent state or sensitive information. For instance, internally, swarm services use tmpfs mounts to mount secrets into a service’s containers.
- **named pipes** - An npipe mount can be used for communication between the Docker host and a container. Common use case is to run a third-party tool inside of a container and connect to the Docker Engine API using a named pipe.

### Docker network

Docker’s networking subsystem is pluggable, using drivers. Several drivers exist by default, and provide core networking functionality:

- **bridge**: The default network driver. If you don’t specify a driver, this is the type of network you are creating. Bridge networks are commonly used when your application runs in a container that needs to communicate with other containers on the same host. See Bridge network driver.

- **host**: Remove network isolation between the container and the Docker host, and use the host’s networking directly. See Host network driver.

- **overlay**: Overlay networks connect multiple Docker daemons together and enable Swarm services and containers to communicate across nodes. This strategy removes the need to do OS-level routing. See Overlay network driver.

- **ipvlan**: IPvlan networks give users total control over both IPv4 and IPv6 addressing. The VLAN driver builds on top of that in giving operators complete control of layer 2 VLAN tagging and even IPvlan L3 routing for users interested in underlay network integration. See IPvlan network driver.

- **macvlan**: Macvlan networks allow you to assign a MAC address to a container, making it appear as a physical device on your network. The Docker daemon routes traffic to containers by their MAC addresses. Using the macvlan driver is sometimes the best choice when dealing with legacy applications that expect to be directly connected to the physical network, rather than routed through the Docker host’s network stack. See Macvlan network driver.

- **none**: Completely isolate a container from the host and other containers. none is not available for Swarm services. See None network driver.

- **Network plugins**: You can install and use third-party network plugins with Docker.

### Logging drivers

- Local file
- Logentries - logging driver sends container logs to the Logentries server.
- JSON File logging driver
- Graylog Extended Format logging driver
- Syslog logging driver
- Amazon CloudWatch Logs logging driver
- ETW logging driver - The ETW logging driver forwards container logs as ETW events. ETW stands for Event Tracing in Windows, and is the common framework for tracing applications in Windows.
- Fluentd logging driver - sends container logs to the Fluentd collector as structured log data.
- Google Cloud Logging driver
- Journald logging driver
- Splunk logging driver - sends container logs to HTTP Event Collector in Splunk Enterprise and Splunk Cloud.
