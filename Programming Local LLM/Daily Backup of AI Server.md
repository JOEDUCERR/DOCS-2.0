## Backup:
* Directory of backup: /var/backups/openwebui (You may access this only using root which means it was set with chmod 700 and not 777)

## Main Setup
1. Create backup directory:
```
sudo mkdir -p /var/backups/openwebui
sudo chmod 700 /var/backups/openwebui
```
2. Create backup script which will run:
```
sudo nano /usr/local/sbin/openwebui-backup
```

```
#!/bin/bash

set -Eeuo pipefail

BACKUP_DIR="/var/backups/openwebui"
VOLUME="open-webui"
CONTAINER="open-webui"
STAMP="$(date '+%Y%m%d-%H%M%S')"
BACKUP_FILE="openwebui-${STAMP}.tar.gz"
BACKUP_PATH="${BACKUP_DIR}/${BACKUP_FILE}"

WAS_RUNNING=0

cleanup() {
    if [ "$WAS_RUNNING" -eq 1 ]; then
        echo "Starting Open WebUI..."
        docker start "$CONTAINER" >/dev/null || \
            echo "WARNING: Failed to restart Open WebUI"
    fi
}

trap cleanup EXIT

echo "========================================"
echo "Open WebUI backup"
echo "Started: $(date)"
echo "========================================"

# Confirm container exists.
if ! docker inspect "$CONTAINER" >/dev/null 2>&1; then
    echo "ERROR: Container '$CONTAINER' does not exist."
    exit 1
fi

# Confirm volume exists.
if ! docker volume inspect "$VOLUME" >/dev/null 2>&1; then
    echo "ERROR: Volume '$VOLUME' does not exist."
    exit 1
fi

# Record whether Open WebUI was running.
if [ "$(docker inspect -f '{{.State.Running}}' "$CONTAINER")" = "true" ]; then
    WAS_RUNNING=1

    echo "Stopping Open WebUI..."
    docker stop "$CONTAINER" >/dev/null
fi

echo "Creating volume backup..."
docker run --rm \
    --mount "type=volume,source=${VOLUME},target=/data,readonly" \
    --mount "type=bind,source=${BACKUP_DIR},target=/backup" \
    alpine:latest \
    sh -c "tar czf '/backup/${BACKUP_FILE}' -C /data ."

echo "Verifying backup archive..."

if [ ! -s "$BACKUP_PATH" ]; then
    echo "ERROR: Backup file does not exist or is empty."
    exit 1
fi

docker run --rm \
    --mount "type=bind,source=${BACKUP_DIR},target=/backup,readonly" \
    alpine:latest \
    sh -c "tar tzf '/backup/${BACKUP_FILE}' >/dev/null"

echo "Backup archive is readable."

# Confirm important Open WebUI data exists in the archive.
docker run --rm \
    --mount "type=bind,source=${BACKUP_DIR},target=/backup,readonly" \
    alpine:latest \
    sh -c "tar tzf '/backup/${BACKUP_FILE}' | grep -qE '(^|/)webui\.db$'"

echo "webui.db found in backup."

# Save the current container configuration.
echo "Saving container configuration..."
docker inspect "$CONTAINER" \
    > "${BACKUP_DIR}/openwebui-container-${STAMP}.json"

chmod 600 "${BACKUP_DIR}/openwebui-container-${STAMP}.json"

# Keep the newest 14 volume backups.
echo "Applying retention policy..."

find "$BACKUP_DIR" \
    -maxdepth 1 \
    -type f \
    -name 'openwebui-*.tar.gz' \
    -printf '%f\n' \
    | sort -r \
    | tail -n +15 \
    | while read -r old_backup; do
        rm -f -- "${BACKUP_DIR}/${old_backup}"
        echo "Removed old backup: ${old_backup}"
      done

# Keep the newest 14 container configuration snapshots.
find "$BACKUP_DIR" \
    -maxdepth 1 \
    -type f \
    -name 'openwebui-container-*.json' \
    -printf '%f\n' \
    | sort -r \
    | tail -n +15 \
    | while read -r old_config; do
        rm -f -- "${BACKUP_DIR}/${old_config}"
        echo "Removed old config: ${old_config}"
      done

echo
echo "Backup completed successfully:"
ls -lh "$BACKUP_PATH"

echo
echo "Finished: $(date)"
```

```
sudo chmod 700 /usr/local/sbin/openwebui-backup
```
3. Create the systemd service so it happens automatically at 3 am:
```
sudo nano /etc/systemd/system/openwebui-backup.service
```

```
[Unit]
Description=Backup Open WebUI data
Requires=docker.service
After=docker.service

[Service]
Type=oneshot
ExecStart=/usr/local/sbin/openwebui-backup
```
4. Create daily timer:
```
sudo nano /etc/systemd/system/openwebui-backup.timer
```

```
[Unit]
Description=Daily Open WebUI backup

[Timer]
OnCalendar=*-*-* 03:00:00
Persistent=true
RandomizedDelaySec=5m

[Install]
WantedBy=timers.target
```

```
sudo systemctl daemon-reload

sudo systemctl enable --now openwebui-backup.timer
```
5. Final Testing:
```
sudo systemctl start openwebui-backup.service

sudo systemctl status openwebui-backup.service

sudo journalctl -u openwebui-backup.service -n 100 --no-pager
```

## Workflow:

![[Main workflow for openwebui backup.png]]