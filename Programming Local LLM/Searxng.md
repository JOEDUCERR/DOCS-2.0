* Setup using docker compose.
* on docker internal network called "openwebui-net" where all open webui, open terminal and searxng-core.
* ipv4 address: 172.19.0.4/16
```
techlead@techlead-ai:~/searxng$ sudo docker compose ps
NAME             IMAGE                              COMMAND                  SERVICE   CREATED          STATUS          PORTS
searxng-core     docker.io/searxng/searxng:latest   "/usr/local/searxng/…"   core      14 minutes ago   Up 8 minutes    0.0.0.0:8080->8080/tcp, [::]:8080->8080/tcp
searxng-valkey   docker.io/valkey/valkey:9-alpine   "docker-entrypoint.s…"   valkey    14 minutes ago   Up 14 minutes   6379/tcp

```

* "http://searxng-core:8080/search?q=<query>" add this link to open webui

![[Network architecture after searxng.png]]