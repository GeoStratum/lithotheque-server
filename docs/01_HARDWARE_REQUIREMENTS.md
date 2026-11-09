# Hardware Sizing & System Requirements Guide

This document outlines the supported architectures and details the **minimum** and **recommended** hardware and software specifications for running **Lithotheque Base Camp Hub** in continuous (24/7) production environments.

---

## 1. Supported Operating Systems & Architectures

Lithotheque Hub is natively compiled without any external runtime or interpreter dependencies (no Python, no JVM, no Node.js):

| Operating System | CPU Architecture | Packaging Format | Execution Mode |
| :--- | :--- | :--- | :--- |
| **Linux (Debian 11+, Ubuntu 20.04+, RHEL 9+)** | `x86_64` (AMD64) | `.deb` package or standalone binary | Native `systemd` daemon |
| **Linux ARM (Debian, Ubuntu, Raspberry Pi OS 64-bit)** | `aarch64` (ARM64) | `.deb` package or standalone binary | Native `systemd` daemon |
| **Windows Server (2019, 2022, 2025) & Windows 10/11** | `x86_64` (x64) | Standalone `.exe` binary | Native Windows Service (`services.msc`) |
| **Windows on ARM (Snapdragon X Elite/Plus, Surface ARM)** | `ARM64` | Standalone `.exe` binary | Native Windows Service (`services.msc`) |

---

## 2. Hardware Profiles: Minimum vs Recommended

### Profile A: Mobile Base Camp Station (Offline Field Expedition)
*Use case: 1 to 10 field geologists or university students connected via a local Wi-Fi router, 4G hotspot, or ad-hoc wireless mesh.*

| Component | Minimum Specification | Recommended Specification |
| :--- | :--- | :--- |
| **Processor (CPU)** | 2 Cores (x86_64 or ARM64)<br>*(e.g., Raspberry Pi 4/5, Intel N100, Celeron)* | 4 Modern Cores<br>*(e.g., Intel Core i3/i5, AMD Ryzen 3, ARM Cortex-A76)* |
| **Memory (RAM)** | **1 GB** free RAM | **2 GB to 4 GB** RAM |
| **System Storage** | 10 GB free space (SSD or high-speed eMMC) | 32 GB to 64 GB NVMe SSD |
| **Network Interface** | 1 Ethernet 100 Mbps or Wi-Fi 802.11n | 1 Wi-Fi 6 (802.11ax) + 1 Gigabit Ethernet port |
| **Power Consumption** | Operable on 12V battery / PowerBank | Rugged low-power Mini-PC (10W - 25W TDP) |

---

### Profile B: Enterprise Central Hub / Regional Headquarters
*Use case: 50 to 500+ concurrent field devices, national consolidation, intensive stereonet calculations, and automated multi-gigabyte GeoPackage exports.*

| Component | Minimum Specification | Recommended Specification |
| :--- | :--- | :--- |
| **Processor (CPU)** | 4 vCPU Cores (2.4 GHz+) | 8 to 16 High-Frequency vCPUs (3.0 GHz+) |
| **Memory (RAM)** | **4 GB** RAM | **16 GB to 32 GB** RAM (optimized for SQLite cache and I/O buffers) |
| **Storage & I/O** | 100 GB SATA SSD (IOPS > 1,000) | 500 GB to 2 TB NVMe SSD in RAID 1 (IOPS > 10,000) |
| **Network Bandwidth** | 100 Mbps symmetrical | 1 Gbps symmetrical |
| **Power & High Availability**| Standard server power supply | Redundant Dual PSU backed by UPS / generator |

---

## 3. Network & Firewall Requirements

For nominal base camp and field synchronization:

| Port | Protocol | Scope | Purpose |
| :--- | :--- | :--- | :--- |
| **`8080/TCP`** | HTTP / REST | Localhost or LAN | Base camp Web Dashboard and management APIs |
| **`51820/UDP`** | WireGuard | Field LAN / Wi-Fi | Secure peer-to-peer mobile device synchronization |
| **`443/TCP`** | HTTPS | Reverse Proxy | Production web access when deployed behind Nginx / Caddy |

---

## 4. Storage Sizing & Photo Backup Estimator

Field data storage requirements depend primarily on high-resolution outcrop photographs:

$$\text{Estimated Storage} = (\text{Field Days} \times \text{Geologists} \times \text{Observations/Day} \times 3.5 \text{ MB}) \times 1.3$$

*Example for a 14-day field expedition with 20 geologists recording 15 outcrops per day:*
- Observations: $14 \times 20 \times 15 = 4,200 \text{ stations}$
- Estimated Storage: $4,200 \times 3.5 \text{ MB} \times 1.3 \approx 19.1 \text{ GB}$ (A 64 GB SSD comfortably handles the entire expedition).
