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

----------------------
# Updating OpenWebUI for v0.12.3

## Current setup

- **Container:** `open-webui`
- **Image track:** `ghcr.io/open-webui/open-webui:main-slim` (rolling development build)
- **Version seen after update:** `v0.12.3`, commit `91ea4e3421d1d8069f13d4a463fcb5986d94ea60`
- **Web port:** `127.0.0.1:3000` → container port `8080`
- **Persistent volume:** `open-webui` → `/app/backend/data`
- **Networks:** `bridge`, `openwebui-net`
- **Host alias:** `host.docker.internal:host-gateway`
- **Restart policy:** `always`

## Important backup and config locations

- **Pre-update data backup:** `/home/techlead/openwebui-pre-main-20261020-003308.tar.gz` (811 MB; gzip integrity check passed).
- **Pre-update config snapshot:** `/home/techlead/open-webui-container-before-update.json` (records the old `v0.11.4` rollback container). Save a fresh snapshot before each future update.
- **Environment file:** `/home/techlead/open-webui-env-before-update.env` (captured from the old `v0.11.4` container for this upgrade). The update steps refresh it from the currently running container. It may contain secrets; keep permissions at `600` and do not share it.
- **Persistent data on disk:** `/var/lib/docker/volumes/open-webui/_data/`
- **Main data:** `webui.db`, `uploads/`, `vector_db/`, `cache/`, and any other files in the volume.

Keep the pre-update archive and the old rollback container until the new build and integrations have been tested. Store an additional copy of important backups on another disk or host if possible; an archive on the same server is not protection against disk failure.

## Update when a new `main-slim` build is wanted

Run these commands on `techlead-ai`. Do not remove Docker volumes.

### 1. Save the current settings

```bash
sudo docker inspect open-webui > "$HOME/open-webui-container-before-update.json"
sudo chmod 600 "$HOME/open-webui-container-before-update.json"

sudo docker inspect open-webui \
  --format '{{range .Config.Env}}{{println .}}{{end}}' \
  > "$HOME/open-webui-env-before-update.env"
chmod 600 "$HOME/open-webui-env-before-update.env"
```

### 2. Stop and back up the data

```bash
BACKUP="$HOME/openwebui-pre-main-$(date +%Y%m%d-%H%M%S).tar.gz"
sudo docker stop open-webui
sudo tar -czf "$BACKUP" -C /var/lib/docker/volumes/open-webui/_data .
sudo chown "$USER":"$(id -gn)" "$BACKUP"

ls -lh "$BACKUP"
gzip -t "$BACKUP" && echo "Backup archive integrity check passed."
sudo tar -tzf "$BACKUP" | grep -E '(^|/)(webui\.db|uploads|vector_db|cache|audit\.log)(/|$)' | head -60
```

**Do not continue unless the archive exists, passes `gzip -t`, and includes the expected data.** Record the exact backup filename for rollback.

### 3. Pull the new image and preserve the current container

```bash
sudo docker pull ghcr.io/open-webui/open-webui:main-slim

# Use a unique name for each update; change the date if this name already exists.
sudo docker rename open-webui "open-webui-rollback-$(date +%Y%m%d)"
```

If the rename says the name already exists, choose a different unique rollback name. Do not delete the old container yet.

### 4. Create the updated container

```bash
sudo docker run -d \
  --name open-webui \
  --restart always \
  -p 127.0.0.1:3000:8080 \
  --add-host=host.docker.internal:host-gateway \
  --env-file "$HOME/open-webui-env-before-update.env" \
  -v open-webui:/app/backend/data \
  ghcr.io/open-webui/open-webui:main-slim

sudo docker network connect openwebui-net open-webui
```

The default `bridge` network is attached automatically. The additional `openwebui-net` connection is required for the current setup.

### 5. Verify

```bash
sudo docker logs --tail 150 open-webui
sudo docker ps -a
sudo docker inspect open-webui --format \
  'Image={{.Config.Image}} ImageID={{.Image}} Status={{.State.Status}} Health={{if .State.Health}}{{.State.Health.Status}}{{else}}No healthcheck{{end}}'
sudo docker inspect open-webui --format '{{json .NetworkSettings.Networks}}'
```

Wait for startup and database migrations to finish. Then test login, chat history, settings, both llama.cpp model connections, Open Terminal, and SearXNG. The current container was reported healthy after the update, but integrations should still be checked in the UI.

## Rollback if the update fails

**Important:** a newer build may migrate the database. Do not start the old image against the updated database. Restore the pre-update archive first.

Replace the example archive path below with the exact backup made for that update, and replace the rollback container name if needed.

```bash
BACKUP="$HOME/openwebui-pre-main-YYYYMMDD-HHMMSS.tar.gz"
ROLLBACK_CONTAINER="open-webui-rollback-YYYYMMDD"

# Stop and remove only the failed new container; do not remove the volume.
sudo docker stop open-webui
sudo docker rm open-webui

# Restore the volume contents to the pre-update state.
sudo find /var/lib/docker/volumes/open-webui/_data -mindepth 1 -maxdepth 1 -exec rm -rf -- {} +
sudo tar -xzf "$BACKUP" -C /var/lib/docker/volumes/open-webui/_data

# Start the previous container/image.
sudo docker start "$ROLLBACK_CONTAINER"
sudo docker ps -a
sudo docker logs --tail 100 "$ROLLBACK_CONTAINER"
```

After restoring, verify login and previous chats. You may need to sign in again because the secret key can be regenerated. Keep the backup until rollback or the upgrade is confirmed successful.

## Notes

- `main-slim` is a **rolling development build**, not a pinned stable release. Use this runbook only when you intentionally want that track.
- Never run `docker volume prune`, delete the `open-webui` volume, or remove rollback containers before verification.
- `open-webui-v0.11.4-rollback` and `open-webui-old` were already present from earlier updates. Keep them until you have confirmed you no longer need them; use unique names for future rollback containers.