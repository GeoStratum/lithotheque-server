# Lithotheque Server (Base Camp Hub)

[![Release](https://img.shields.io/badge/Release-v1.0.0-blue.svg)](https://github.com/GeoStratum/lithotheque-server/releases)
[![License: Proprietary](https://img.shields.io/badge/License-Commercial-green.svg)](#licensing)
[![Platform](https://img.shields.io/badge/Platform-Windows%20%7C%20Linux%20(x64%20%26%20ARM64)-orange.svg)](#system-requirements)

**Lithotheque Server** is the sovereign, offline-first base camp hub designed for geological expeditions, academic field courses, and geotechnical survey teams. It enables instant, seamless synchronization of field observations, photographs, structural geology measurements, and GIS project consolidation—**with zero reliance on internet connectivity or cloud infrastructure**.

---

## Key Features

- **100% Autonomous & Offline:** Operates anywhere (remote valleys, quarries, forests, underground) on a standard laptop, mini-PC, or server.
- **Frictionless Mobile Pairing:** Field technicians and students connect instantly by scanning an on-screen pairing QR code using the Lithotheque mobile application. No user accounts, credentials, or emails required on the field.
- **Enterprise Network Friendly:** Operates safely alongside institutional networks, personal hotspots, and managed enterprise devices without routing or connectivity conflicts.
- **Centralized Master Map & Real-Time Stereonet:** Displays incoming field observations in real time on an interactive base camp dashboard with geological symbols and structural projections.
- **One-Click Master GIS Exports:** Consolidates field data from all active teams into industry-standard GIS formats ready for immediate analysis in **QGIS** or **ArcGIS**.
- **Multi-OS & Cross-Architecture:** Runs natively as an automated background service on **Debian/Ubuntu Linux** and **Windows (10/11/Server)**, supporting both **Intel/AMD (x64)** and **ARM64** platforms.

---

## User Documentation

- [Getting Started & Installation Guide](docs/GETTING_STARTED.md)
- [Base Camp Operations & GIS Exports](docs/USER_GUIDE.md)
- [Licensing & Activation](docs/LICENSING.md)

---

## System Requirements

| Specification | Minimum Requirement | Recommended (up to 40 field peers) |
| :--- | :--- | :--- |
| **Operating System** | Ubuntu 22.04+, Debian 11+, Windows 10/11, Windows Server 2019+ | Modern Linux Server or Windows 11 / Server |
| **Architecture** | 64-bit x86 (`x64`/`amd64`) or ARM64 (`aarch64`) | `x64` or `arm64` (e.g. Snapdragon X Elite, Raspberry Pi 5) |
| **Processor** | 1 Core | 2 Cores |
| **RAM** | 1 GB | 2 GB to 4 GB |
| **Disk Space** | 10 GB SSD | 50 GB SSD (for full multi-day photo backups) |
| **Network** | Local Wi-Fi router, ad-hoc hotspot, or Ethernet switch | Standard field Wi-Fi router / mobile hotspot |

---

## Supported GIS & Data Formats

Lithotheque Server consolidates observations into the following open formats:
- **Master GeoPackage (`.gpkg`):** Consolidated SQLite-based GIS project styled for direct import into QGIS.
- **GeoJSON (`.geojson`):** Standard web mapping vector layers.
- **Tabular Spreadsheet (`.csv`):** Comprehensive field attribute and structural tables for Excel, R, and Python.
- **Garmin GPS (`.gpx`):** Waypoints and tracks for handheld outdoor navigation devices.
- **Google Earth (`.kml`):** 3D geological field views.
- **Full Field Archive (`.zip`):** High-resolution calibrated photos, geological sketches, and raw metadata backup.

---

## Commercial Licensing

Lithotheque Server is distributed under annual or perpetual institutional licenses. Each license is tied to an organization name and includes an authorized concurrent device quota.

To request a formal quote, purchase order invoice, or evaluation access, please contact GeoStratum at [support@geostratum.eu](mailto:support@geostratum.eu).
