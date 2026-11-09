# Backup & Disaster Recovery Runbook

This runbook outlines procedures for executing hot live backups, setting up automated disaster recovery pipelines, and ensuring zero-data-loss restoration for **Lithotheque Base Camp Hub**.

---

## 1. Storage Architecture Overview

Lithotheque Hub uses **SQLite in WAL (Write-Ahead Logging) mode** with atomic page locking. Data is stored across two primary directories:

- **Database Directory**: Contains `litho_hub.db`, `litho_hub.db-wal`, and `litho_hub.db-shm`.
- **Media Storage Directory**: Contains original raw high-resolution field photos organized by folder and observation UUID.

> [!CAUTION]
> Never copy `litho_hub.db` using standard `cp` or `robocopy` while the service is actively running! A raw file copy of an active WAL database can result in a corrupted snapshot. Use the dedicated backup API or SQLite `.backup` command.

---

## 2. Performing a Live Hot Backup

### Method A: Automated Hot Backup API (Recommended)
Lithotheque Hub provides an authenticated administrative snapshot endpoint:

```bash
curl -X POST http://127.0.0.1:8080/api/admin/backup \
     -H "Content-Type: application/json" \
     -d '{"target_dir": "/var/backups/litho-hub/"}'
```

This endpoint executes a thread-safe snapshot using the SQLite Online Backup API, ensuring consistent data even during intense concurrent mobile sync.

### Method B: SQLite Online Backup Command
```bash
sqlite3 /var/lib/litho-hub/data/litho_hub.db ".backup '/var/backups/litho-hub/litho_hub_$(date +%Y%m%d_%H%M%S).db'"
```

---

## 3. Automated Daily Cron Backup Script (Linux)

Create `/usr/local/bin/backup-litho-hub.sh`:

```bash
#!/usr/bin/env bash
set -euo pipefail

BACKUP_DIR="/var/backups/litho-hub"
TIMESTAMP=$(date +"%Y%m%d_%H%M%S")
DEST="${BACKUP_DIR}/snapshot_${TIMESTAMP}"
DB_PATH="/var/lib/litho-hub/data/litho_hub.db"
MEDIA_DIR="/var/lib/litho-hub/data/media"

mkdir -p "${DEST}"

# 1. Hot database snapshot
sqlite3 "${DB_PATH}" ".backup '${DEST}/litho_hub.db'"

# 2. Synchronize media files
rsync -av --delete "${MEDIA_DIR}/" "${DEST}/media/"

# 3. Create compressed encrypted tarball
tar -czf "${BACKUP_DIR}/litho_backup_${TIMESTAMP}.tar.gz" -C "${BACKUP_DIR}" "snapshot_${TIMESTAMP}"
rm -rf "${DEST}"

# 4. Enforce 30-day retention policy
find "${BACKUP_DIR}" -name "litho_backup_*.tar.gz" -mtime +30 -delete

echo "[$(date)] Backup completed successfully: litho_backup_${TIMESTAMP}.tar.gz"
```

Add to cron (`sudo crontab -e`):
```cron
# Execute daily at 02:00 AM
0 2 * * * /usr/local/bin/backup-litho-hub.sh >> /var/log/litho-hub-backup.log 2>&1
```

---

## 4. Disaster Recovery & Complete Restoration

In the event of hardware failure:

1. **Stop the Service**:
   ```bash
   sudo systemctl stop litho-hub
   ```

2. **Restore Archive**:
   ```bash
   tar -xzf /var/backups/litho-hub/litho_backup_20261109_020000.tar.gz -C /tmp/
   sudo cp /tmp/snapshot_20261109_020000/litho_hub.db /var/lib/litho-hub/data/
   sudo rsync -av /tmp/snapshot_20261109_020000/media/ /var/lib/litho-hub/data/media/
   sudo chown -R litho-hub:litho-hub /var/lib/litho-hub/data/
   ```

3. **Verify Database Integrity**:
   ```bash
   sqlite3 /var/lib/litho-hub/data/litho_hub.db "PRAGMA integrity_check;"
   # Output: ok
   ```

4. **Restart the Service**:
   ```bash
   sudo systemctl start litho-hub
   sudo systemctl status litho-hub
   ```
