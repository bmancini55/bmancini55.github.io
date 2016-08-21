# DOCKER CHEATSHEET

## Docker Machine

```bash 
# List docker machines
docker-machine ls
```

```bash
# Get IP of docker machine
docker-machine ip <machine_name>
docker-machine ip default
```

```bash
# Display environemnt information for a machine
docker-machine env default

# Setup bash for a machine > executes the above statement
eval "$(docker-machine env default)"
```


## Docker Commands

You can run `docker` to see the full list of commands.  

### Managing Images
```bash
# List images
docker images
```

```bash
# Remove an image
docker rmi <image_name>

# Remove <none> images
docker rmi $(docker images -a | grep "^<none>" | awk '{print $3}')
```

### Managing Containers

```bash
# List running containers
docker ps

# List running and stopped containers
docker ps --all
```

```bash
# Details of a container
docker inspect <container_name>
```

```bash
# Stop a container
docker stop <container_name>
```

```bash
# Remove a container
docker rm <container_name>

# Stop and remove a container
docker rm -f <container_name>

# Remove stopped containers
docker rm $(docker ps -a -q)

# Remove all containers of a specific image
docker rm $(docker ps --all | grep <image_name> | awk '{ print $1 }')
docker rm $(docker ps --all | grep consul | awk '{ print $1 }')
```

```bash
# Attach shell to a running container
docker exec -it <container_name> /bin/bash
```

### Docker Run

Full reference - https://docs.docker.com/engine/reference/run/

Common switches:
```
--name containerName
--rm cleanup after shutdown
-v mount volume host:container (ex: "$PWD":/usr/src/test)
-d detached mode (daemon mode)
-p expose incoming ports host:container (ex: 8080:8080)
-P expose all exported ports mapped to random ports on host
-t allocate a TTY
-i keep STDIN open even if not attached
-a attach to STDIN, STDOUT, STDERR
-e environment variables
--net="bridge"
--net="host"
```

```bash
# Name a container when you run it
docker run --name <friendly_name> <image_name>
docker run --name my-nginx nginx 

# Run a container in interactive mode
docker run -it <image_name>
docker run -it nginx

# Run a container as a daemon
docker run -d <image_name>
docker run -d nginx

# Expose all the ports for a container
docker run -P <image_name>
docker run -P nginx

# Expose a mapped port on the host
docker run -p <host_port>:<container_port> <image_name>
docker run -p 80:80 nginx

# Expose multiple mapped ports on the host
docker run -p <host_port>:<container_port> -p <host_port>:<container_port> <image_name>
docker run -p 80:80 -p 443:443 nginx

# Start container with environment variables
docker run -e "<environment_variable_expression>" <image_name>
docker run -e "BROKER_PATH=amqp:192.168.99.100" my-gateway

# Mount a directory from the host to the container
docker run -v <host_path>:<container_path>:<permission:rw,ro> <image_name>
docker run -v /var/www:/usr/share/nginx/html:ro nginx
docker run -v /var/www:/usr/share/nginx/html nginx

# Mount multiple directories from the host to the container
docker run -v <host_path>:<container_path>:<permission:rw,ro>  -v <host_path>:<container_path>:<permission:rw,ro> <image_name>
docker run -v /var/www:/usr/share/nginx/html:ro -v /var/nginx/conf:/etc/nginx:ro nginx
docker run -v /var/www:/usr/share/nginx/html -v /var/nginx/conf:/etc/nginx nginx

# Mount volume from current directory
docker run -v "$PWD":<container_name>:<permission:rw,ro> <image_name>
docker run -v "$PWD":/usr/src/test node
```


### Docker Build Commands
```bash
# With Dockerfile
docker build -t <container_name> .
docker build -t my-nodejs-app

# With named Dockerfile
docker build -f <dockerfile_name> -t <container_name> .
docker build -f Dockerfile-gateway -t my-gateway .

# Without Dockerfile
docker build -t
```

With the bridged network adapter, inside a container the following can be done to obtain its IP address:
```bash
# Getting the bridge IP
/sbin/ip route|awk '/default/ { print $3 }'

which can be passed as an environment variable:
BRIDGE_IP=`/sbin/ip route|awk '/default/ { print $3 }'` node test.js
```


## Pushing to Docker Hub

Pushing to Docker Hub - https://docs.docker.com/mac/step_six/

``` bash
$ docker images
$ docker tag 3d8779d32025 bmancini55/ms-gateway:latest
$ docker login --username=bmancini55 --email=bmancini@gmail.com
$ docker push bmancini55/ms-gateway
```
