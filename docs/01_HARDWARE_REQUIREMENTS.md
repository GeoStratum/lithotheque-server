# Guide de Dimensionnement Matériel & Prérequis Système

Ce document spécifie les architectures cibles supportées, ainsi que les exigences matérielles et logicielles **minimales** et **recommandées** pour l'hébergement continu (24/7) de **Lithotheque Base Camp Hub**.

---

## 1. Systèmes d'Exploitation & Architectures Supportés

Lithotheque Hub est compilé nativement sans runtime externe ni interpréteur (pas de Python, pas de JVM, pas de Node.js) :

| Système d'Exploitation | Architecture CPU | Format de Paquet | Mode d'Exécution |
| :--- | :--- | :--- | :--- |
| **Linux (Debian 11+, Ubuntu 20.04+, RHEL 9+)** | `x86_64` (AMD64) | Paquet `.deb` ou binaire ELF | Service natif `systemd` (daemon) |
| **Linux ARM (Debian, Ubuntu, Raspberry Pi OS 64-bit)** | `aarch64` (ARM64) | Paquet `.deb` ou binaire ELF | Service natif `systemd` (daemon) |
| **Windows Server (2019, 2022, 2025) & Windows 10/11** | `x86_64` (x64) | Installateur `.msi` | Service Windows natif (`services.msc`) |
| **Windows on ARM (Snapdragon X Elite/Plus, Surface ARM)** | `ARM64` | Installateur `.msi` (ARM64) | Service Windows natif (`services.msc`) |

---

## 2. Profils Matériels : Minimal vs Recommandé

### Profil A : Station de Camp de Base Mobile (Terrain Hors-Ligne)
*Cas d'usage : 1 à 10 géologues sur le terrain connectés via point d'accès Wi-Fi 4G/satellite ou liaison locale ad-hoc.*

| Composant | Configuration Minimale | Configuration Recommandée |
| :--- | :--- | :--- |
| **Processeur (CPU)** | 2 cœurs (x86_64 ou ARM64)<br>*(ex: Raspberry Pi 4/5, Intel N100, Celeron)* | 4 cœurs récents<br>*(ex: Intel Core i3/i5, AMD Ryzen 3, ARM Cortex-A76)* |
| **Mémoire Vive (RAM)** | **1 Go** de RAM disponible | **2 Go à 4 Go** de RAM |
| **Stockage Système** | 10 Go d'espace libre (SSD ou eMMC haute vitesse) | 32 Go à 64 Go SSD NVMe |
| **Réseau** | 1 interface Ethernet 100 Mbps ou Wi-Fi 802.11n | 1 interface Wi-Fi 6 (802.11ax) + 1 port Gigabit Ethernet |
| **Consommation** | Fonctionnement sur batterie 12V / PowerBank possible | Mini-PC durci basse consommation (10W - 25W TDP) |

---

### Profil B : Serveur d'Entreprise Centralisé / Datacenter (Cloud & On-Premise)
*Cas d'usage : 50 à 500+ collaborateurs simultanés, consolidations nationales, projections stéréographiques intensives, exports GeoPackage massifs.*

| Composant | Configuration Minimale | Configuration Recommandée |
| :--- | :--- | :--- |
| **Processeur (CPU)** | 4 cœurs vCPU (2.4 GHz+) | 8 à 16 vCPU haute fréquence (3.0 GHz+) |
| **Mémoire Vive (RAM)** | **4 Go** de RAM | **16 Go à 32 Go** de RAM (pour cache SQLite et buffers I/O) |
| **Stockage & I/O** | 100 Go SSD SATA (IOPS > 1 000) | 500 Go à 2 To SSD NVMe en RAID 1 (IOPS > 10 000) |
| **Bande Passante** | 100 Mbps symétrique | 1 Gbps symétrique |
| **Disponibilité / Alimentation** | Alimentation standard | Double alimentation redondante (UPS / Onduleur secouru) |

---

## 3. Prérequis Réseau et Pare-Feu

Pour un fonctionnement 100% nominal :
* **Flux UDP 51820 (Entrant)** :
  * Indispensable pour le canal sécurisé WireGuard de synchronisation directe terrain.
  * Doit être ouvert dans les pare-feux réseau ou redirigé (NAT/Port Forwarding) vers la station hôte.
* **Flux TCP 80 & 443 (Entrant)** :
  * Réservé au Reverse Proxy (Nginx / Caddy) pour l'accès HTTPS au tableau de bord géologique et aux exports.
* **Flux TCP 8080 (Interne Localhost)** :
  * Ne doit **JAMAIS** être exposé directement sur Internet public sans reverse proxy.
