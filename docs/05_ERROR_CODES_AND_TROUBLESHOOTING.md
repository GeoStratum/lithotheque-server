# Error Codes & Troubleshooting Reference

This document provides an exhaustive reference of error codes, diagnostic recipes, and resolution procedures for **Lithotheque Base Camp Hub**.

---

## 1. System Error Code Catalog

### Licensing & Activation Codes (`ERR_LIC_*`)

| Code | Severity | Description | Resolution |
| :--- | :--- | :--- | :--- |
| **`ERR_LIC_NOT_FOUND`** | Critical | `license.key` file is missing from the configured path. | Place a valid `license.key` into `/etc/litho-hub/` or `C:\ProgramData\Lithotheque Hub\`. |
| **`ERR_LIC_INVALID_SIG`** | Critical | Cryptographic signature verification failed (Ed25519/Dilithium). | File was corrupted or modified. Redownload the untouched key from your GeoStratum portal. |
| **`ERR_LIC_EXPIRED`** | High | Annual subscription license period has elapsed. | Contact GeoStratum sales (`sales@geostratum.eu`) to renew your subscription. |
| **`ERR_LIC_PEER_QUOTA`** | Medium | Active connected devices exceed licensed capacity (`max_peers`). | Upgrade your commercial tier (e.g., from *Team* [5] to *Base Camp* [20] or *Promotion* [50]). |

---

### Networking & WireGuard Codes (`ERR_WG_*`)

| Code | Severity | Description | Resolution |
| :--- | :--- | :--- | :--- |
| **`ERR_WG_PORT_BIND`** | High | Port `51820/UDP` is already in use by another service. | Identify the conflicting process (`lsof -i :51820` or `netstat -ano`) or alter `wireguard_port` in `config.json`. |
| **`ERR_WG_PEER_HANDSHAKE`**| Medium | Mobile client scanned the QR code but handshake failed. | Check firewall rules: ensure `51820/UDP` is permitted in UFW/Windows Defender. |
| **`ERR_WG_IP_EXHAUSTION`** | High | Internal subnet (`10.42.0.0/24`) has run out of available addresses. | Restart service to clear expired dynamic peer leases. |

---

### Database & Storage Codes (`ERR_DB_*`)

| Code | Severity | Description | Resolution |
| :--- | :--- | :--- | :--- |
| **`ERR_DB_LOCKED`** | Medium | SQLite database busy timeout (> 5000ms) under heavy write load. | Verify storage disk IOPS. Ensure the database is hosted on an SSD, not a slow USB flash drive. |
| **`ERR_DB_DISK_FULL`** | Critical | Insufficient disk storage space remaining on host volume. | Free up disk space or prune old consolidated export archives in `/exports/`. |
| **`ERR_DB_CORRUPT`** | Critical | SQLite page corruption detected by SQLite engine. | Restore the latest healthy backup using the [Disaster Recovery Runbook](04_BACKUP_AND_DISASTER_RECOVERY.md). |

---

## 2. Troubleshooting Diagnostic Recipes

### Problem: Field tablet camera scans QR code, but synchronization does not start
1. Verify the WireGuard UDP listener is active on the server:
   ```bash
   sudo ss -u -l -p -n | grep 51820
   ```
2. Verify local firewall permissions:
   ```bash
   sudo ufw status | grep 51820
   ```
3. Ensure both the server and mobile devices are connected to the exact same Wi-Fi subnet or local ad-hoc access point with client isolation disabled.

### Problem: Web dashboard displays "Licence: Vérification..." indefinitely
1. Check that the service process has read permissions on `license.key`:
   ```bash
   ls -la /etc/litho-hub/license.key
   # Expected: -rw------- 1 litho-hub litho-hub
   ```
2. View service startup log messages:
   ```bash
   sudo journalctl -u litho-hub -n 50 --no-pager
   ```
