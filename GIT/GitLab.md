https://dev.to/ishmam_abir/set-up-your-own-local-gitlab-server-self-hosted-gitlab-4d1

* Open source Git hosting solution.
* Basically, Self-hosted system for code management to improve DevOps adoption.

### Commands to set it up
```
docker pull gitlab/gitlab-ce:latest

mkdir -p /code/gitlab/config /code/gitlab/logs /code/gitlab/data

docker run --detach 
    --hostname localhost 
    --publish 8443:443 
    --publish 8080:80 
    --publish 6022:22 
    --name gitlab 
    --restart always 
    --volume code/gitlab/config:/etc/gitlab 
    --volume code/gitlab/logs:/var/log/gitlab 
    --volume code/gitlab/data:/var/opt/gitlab 
    gitlab/gitlab-ce
    
    # Check logs
    docker logs -f gitlab
    
    # Get initial root password (valid for 24 hrs)
    docker exec -it gitlab cat /etc/gitlab/initial_root_password
```
[[GitLab Local Hosting]]