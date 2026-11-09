# Installation & Operations Manual

This guide provides end-to-end instructions for deploying, operating, and maintaining **Lithotheque Base Camp Hub** across Debian/Ubuntu Linux and Microsoft Windows environments.

---

## 1. Linux Installation (Debian / Ubuntu)

### Step 1: Package Installation
Download the official `.deb` package matching your server's architecture from the [Releases page](https://github.com/GeoStratum/lithotheque-server/releases):

```bash
# For Intel / AMD 64-bit servers:
sudo dpkg -i litho-hub_v1.0.0_amd64.deb

# For ARM64 servers (Raspberry Pi 4/5, Graviton, Ampere):
sudo dpkg -i litho-hub_v1.0.0_arm64.deb
```

The Debian package automatically configures:
- Binary deployed to `/usr/local/bin/litho-hub`.
- Dedicated system service user `litho-hub`.
- Configuration and license directory at `/etc/litho-hub/`.
- Data and GIS export directory at `/var/lib/litho-hub/data/`.
- Systemd service unit at `/etc/systemd/system/litho-hub.service`.

### Step 2: License Deployment
Deposit your signed `license.key` into `/etc/litho-hub/` and assign permissions:

```bash
sudo cp license.key /etc/litho-hub/license.key
sudo chown -R litho-hub:litho-hub /etc/litho-hub/
sudo chmod 600 /etc/litho-hub/license.key
```

### Step 3: Service Management
```bash
# Enable automatic startup on boot
sudo systemctl enable litho-hub

# Start the service
sudo systemctl start litho-hub

# Check operational status
sudo systemctl status litho-hub

# Stream real-time operational logs
sudo journalctl -u litho-hub -f
```

---

## 2. Microsoft Windows Deployment

### Step 1: Directory Setup
Create the standard application directories in PowerShell (Run as Administrator):

```powershell
New-Item -ItemType Directory -Force -Path "C:\Program Files\Lithotheque Hub"
New-Item -ItemType Directory -Force -Path "C:\ProgramData\Lithotheque Hub\data"
New-Item -ItemType Directory -Force -Path "C:\ProgramData\Lithotheque Hub\exports"
```

### Step 2: Binary & License Placement
```powershell
# Copy executable and license
Copy-Item litho-hub.exe "C:\Program Files\Lithotheque Hub\"
Copy-Item license.key "C:\ProgramData\Lithotheque Hub\license.key"
```

### Step 3: Configuration File (`config.json`)
Create `C:\ProgramData\Lithotheque Hub\config.json`:

```json
{
  "listen_addr": "127.0.0.1:8080",
  "data_dir": "C:\\ProgramData\\Lithotheque Hub\\data",
  "export_dir": "C:\\ProgramData\\Lithotheque Hub\\exports",
  "license_path": "C:\\ProgramData\\Lithotheque Hub\\license.key",
  "wireguard_port": 51820
}
```

### Step 4: Register as a Windows Service (Session 0)
Using PowerShell:

```powershell
New-Service -Name "LithoHub" `
            -BinaryPathName '"C:\Program Files\Lithotheque Hub\litho-hub.exe" -config "C:\ProgramData\Lithotheque Hub\config.json"' `
            -DisplayName "Lithotheque Base Camp Hub" `
            -Description "Lithotheque Sovereign Field Synchronization Service" `
            -StartupType Automatic

Start-Service -Name "LithoHub"
Get-Service -Name "LithoHub"
```

---

## 3. Operational Health Checks

Verify that the local hub is healthy and operational:

```bash
curl -s http://127.0.0.1:8080/api/health
# Response: {"status":"healthy","uptime_seconds":42}

curl -s http://127.0.0.1:8080/api/status | jq .
```
