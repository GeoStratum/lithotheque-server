# Getting Started & Installation Guide

This guide describes how to install and start **Lithotheque Server** on your base camp computer.

---

## 1. Installation on Linux (Ubuntu / Debian)

Lithotheque Server is distributed as standard native `.deb` packages for both x86_64 and ARM64 architectures.

### Step 1: Install the Package
Open a terminal and run the installation command matching your hardware:

```bash
# For Intel / AMD computers (x64)
sudo dpkg -i litho-hub_*_amd64.deb

# For ARM64 systems (Raspberry Pi 4/5, ARM servers)
sudo dpkg -i litho-hub_*_arm64.deb
```

The package automatically installs the daemon, registers the background service (`litho-hub.service`), and creates desktop integration shortcuts.

### Step 2: Install your License Key
Copy your official `license.key` file provided by GeoStratum into the server configuration directory:

```bash
sudo cp license.key /etc/litho-hub/license.key
sudo chmod 600 /etc/litho-hub/license.key
```

### Step 3: Start the Service
```bash
sudo systemctl daemon-reload
sudo systemctl restart litho-hub
```

### Step 4: Access the Dashboard
- **Web Browser:** Open `http://localhost:8080`.
- **Desktop (Ubuntu GUI):** Click on the **Lithotheque Hub** icon in your application launcher.

---

## 2. Installation on Windows (Windows 10, 11 & Windows Server)

Lithotheque Server is distributed as native Windows Installer (`.msi`) packages.

### Step 1: Run the Installer
Launch the installer matching your processor:
- **`litho-hub_*_x64.msi`** for standard Intel or AMD PCs and servers.
- **`litho-hub_*_arm64.msi`** for Windows on ARM devices (e.g., Qualcomm Snapdragon X Elite, Microsoft Surface Pro ARM).

Follow the setup wizard. The installer automatically configures the background service and sets appropriate local firewall allowances.

### Step 2: Install your License Key
Place your `license.key` file in the shared application data folder:
```text
C:\ProgramData\Lithotheque Hub\license.key
```

### Step 3: Restart the Service
Open PowerShell as Administrator and run:
```powershell
Restart-Service -Name "LithoHubService"
```

### Step 4: Open the Dashboard
- Double-click the **Lithotheque Hub** shortcut created on your Desktop or Start Menu.
- Or open your web browser to `http://localhost:8080`.
