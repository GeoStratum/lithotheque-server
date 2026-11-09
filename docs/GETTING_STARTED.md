# Getting Started with Lithotheque Hub

Welcome to **Lithotheque Base Camp Hub** (`litho-hub`). This guide walks field directors, geological survey leaders, and IT administrators through rapid setup in under 5 minutes.

---

## 1. Quick Architecture Concept

```
┌─────────────────────────────────┐        Local Wi-Fi / Hotspot        ┌───────────────────────────────────┐
│     Android Field Tablets       │ ──────────────────────────────────> │    Lithotheque Base Camp Hub      │
│   (Lithotheque Mobile App)      │    Secure WireGuard (51820/UDP)     │ (Laptop / Raspberry Pi / Server)  │
└─────────────────────────────────┘                                     └─────────────────┬─────────────────┘
                                                                                          │
                                                                                          ▼
                                                                        ┌───────────────────────────────────┐
                                                                        │    Master GIS Consolidations      │
                                                                        │  GeoPackage, GeoJSON, CSV, KML    │
                                                                        └───────────────────────────────────┘
```

---

## 2. Fast Setup Steps

### 1. Download
Grab the latest release for your platform from the [Releases page](https://github.com/GeoStratum/lithotheque-server/releases/latest).

### 2. Install
- **Linux (Debian/Ubuntu)**:
  ```bash
  sudo dpkg -i litho-hub_v1.0.0_amd64.deb
  ```
- **Windows**:
  Download `litho-hub.exe` and place it in your chosen working directory.

### 3. Place your License Key
Place your `license.key` provided by GeoStratum into:
- Linux: `/etc/litho-hub/license.key`
- Windows: `C:\ProgramData\Lithotheque Hub\license.key`

*(Note: Without a license key, the Hub operates in Demonstration Mode with a 3-device concurrent limit).*

### 4. Start the Service
- **Linux**:
  ```bash
  sudo systemctl start litho-hub
  ```
- **Windows**:
  Run `.\litho-hub.exe -config config.json` in PowerShell.

### 5. Open the Dashboard
Navigate to `http://localhost:8080` in your web browser. You will see:
- Real-time connected peer count.
- The interactive base camp map with BRGM geological layer overlays.
- Real-time stereographic projection canvas.
- The **Enrollment QR Code** for mobile tablets.

---

## 3. Connecting Field Geologists

1. Open the **Lithotheque** application on the Android device.
2. Navigate to **Base Camp Sync**.
3. Tap **Scan Base Camp QR Code**.
4. Point the camera at the QR code displayed on the Hub dashboard screen.
5. Synchronization initiates immediately: observations, structural planes, and outcrop photographs begin transferring over the local wireless link automatically.
