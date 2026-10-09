## Access

* Web (HTTP): http://localhost:8080
* Web (HTTPS): https://localhost:8443
* SSH port: 6022

## Start / stop

```
docker start gitlab       # Start the existing container
docker stop gitlab        # Stop GitLab safely
docker restart gitlab     # Restart it
```

```
docker ps -a --filter name=gitlab   # Check container status
docker logs --tail 100 gitlab       # View recent logs
```

Docker is configured with --restart always, so the container should start automatically when Docker starts. To start Docker itself:

```
sudo systemctl start docker
sudo systemctl status docker
```
## Where GitLab data is stored

These host directories are mounted into the container, so the data remains on the host when the container is stopped:

* /code/gitlab/config → /etc/gitlab (configuration)
* /code/gitlab/logs → /var/log/gitlab (logs)
* /code/gitlab/data → /var/opt/gitlab (repositories and application data)

## Important

Use docker stop gitlab and docker start gitlab for normal shutdown/startup. Do not run the original docker run command again unless the container has been removed; it creates a new container and the name gitlab is already in use while the existing one exists.