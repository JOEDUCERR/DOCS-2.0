* docker pull image_name: pull a docker image from the official docker image repository
* docker images: list docker images
* docker image inspect image_name: check all kinds of details about the image (can also see ports which will be exposed)
* docker system df: Too see specs of all docker related files in system
* docker ps: see all running containers
* docker ps -a: see all containers exited and running
* docker logs container_name
* docker inspect container_name
* docker stats container_name: shows resources used by the container

## Mounting a folder from host to docker container
Types of binds:
1. **Bind mounts:** Connect a directory or file on your host to a path inside a container.
2. **Named volumes:** Let Docker manage storage that persists independently of a particular container's life cycle.

### Bind Mount
```
mkdir -p ~/docker-lab/site
cd ~/docker-lab/site

echo "<h1>My Docker Lab</h1>" > index.html

docker run -d \
  --name docker-lab-web \
  -p 8080:80 \
  --mount type=bind,source="$HOME/docker-lab/site",target=/usr/share/nginx/html,readonly \
  nginx:alpine
```

### Named Mount
```
docker volume create pg-lab-data

docker run -d \
  --name pg-lab \
  -e POSTGRES_PASSWORD=labpass \
  -e POSTGRES_DB=lab \
  -v pg-lab-data:/var/lib/postgresql/data \
  postgres:16
```