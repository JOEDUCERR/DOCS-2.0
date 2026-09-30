## Main Steps:
* Confirm if backend data and volume is present. (OpenWebUI is hosted in docker locally so check the persistent volumes).
* Check Mounts, Networks, Ports, Restart Policy, Compose Labels and the WebUI secret key. Also, Env Variables, Extra hosts, Command and Entry point.
* Make a complete backup of the following while OpenWebUI is stopped.
```
webui.db
uploads/
vector_db/
cache/
audit.log
```
* Check which type of image you are using and update to that type only (using main-slim).
* Basically : **backup → stop → verify backup → pull new image → recreate with the same volume/networks/settings → verify users/chats/models/Open Terminal/SearXNG → rollback procedure**.
## Practical Implementation:
1. Save the current container config in two files containing config and one containing env variables:
```
sudo docker inspect open-webui > ~/open-webui-container-before-update.json

sudo docker inspect open-webui \
  --format '{{range .Config.Env}}{{println .}}{{end}}' \
  > ~/open-webui-env-before-update.env

chmod 600 ~/open-webui-env-before-update.env
chmod 600 ~/open-webui-container-before-update.json
```
2. Stop open webui:
```
sudo docker stop open-webui
```
3. Backup up actual Open WebUI volume:
```
sudo tar -czf "$HOME/openwebui-backup-$(date +%Y%m%d-%H%M%S).tar.gz" -C /var/lib/docker/volumes/open-webui/_data .

#Check if files is present
ls -lh ~/openwebui-backup-*.tar.gz

sudo tar tzf ~/openwebui-backup-*.tar.gz | head -30
```
4. Pull the new image:
```
sudo docker pull ghcr.io/open-webui/open-webui:v0.11.4-slim
```
5. Rename the old container:
```
sudo docker rename open-webui open-webui-old
```
6. Create the new open webui container and attach other two networks:
```
sudo docker run -d \
  --name open-webui \
  --restart always \
  -p 127.0.0.1:3000:8080 \
  --add-host=host.docker.internal:host-gateway \
  --network bridge \
  --env-file ~/open-webui-env-before-update.env \
  -v open-webui:/app/backend/data \
  ghcr.io/open-webui/open-webui:v0.11.4-slim
  
sudo docker network connect ai-net open-webui
sudo docker network connect openwebui-net open-webui

#Check network
sudo docker inspect open-webui \
  --format '{{range $name, $v := .NetworkSettings.Networks}}{{println $name}}{{end}}'
```
7. Watch startup logs:
```
sudo docker logs -f open-webui

sudo docker ps
```