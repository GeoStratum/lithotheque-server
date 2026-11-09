# Guide d'Installation et d'Exploitation 24/7

Ce document décrit en détail la procédure d'installation, de configuration et de supervision continue de **Lithotheque Base Camp Hub** sous Linux et Windows.

---

## 1. Déploiement sous Linux (Debian / Ubuntu / Raspberry Pi OS)

### A. Installation du paquet `.deb`

```bash
# Pour architecture x86_64
sudo dpkg -i litho-hub_1.0.0_amd64.deb

# Pour architecture ARM64 (ex: Raspberry Pi 5 ou serveurs Graviton)
sudo dpkg -i litho-hub_1.0.0_arm64.deb

# Résolution des dépendances système si nécessaire
sudo apt-get install -f -y
```

### B. Emplacements Système
* **Binaire exécutable** : `/usr/local/bin/litho-hub`
* **Répertoire de configuration** : `/etc/litho-hub/`
* **Fichier de configuration** : `/etc/litho-hub/config.json`
* **Licence cryptographique Ed25519** : `/etc/litho-hub/license.key`
* **Base de données géologique** : `/etc/litho-hub/data/litho_hub.db`
* **Dossier des exports géologiques** : `/etc/litho-hub/exports/`
* **Service systemd** : `/etc/systemd/system/litho-hub.service`

### C. Gestion du Service systemd (Headless 24/7)
Le service est configuré pour démarrer automatiquement et redémarrer instantanément en cas de défaillance.

```bash
# Vérifier l'état du service
sudo systemctl status litho-hub

# Démarrer ou redémarrer le service
sudo systemctl restart litho-hub

# Activer au démarrage système
sudo systemctl enable litho-hub

# Suivre les journaux d'activité en temps réel
sudo journalctl -u litho-hub -f --no-tail
```

---

## 2. Déploiement sous Windows & Windows Server

### A. Installation via le paquet `.msi`
L'installateur peut être exécuté graphiquement ou de manière entièrement silencieuse pour les déploiements d'entreprise (GPO / Intune / SCCM) :

```powershell
# Installation silencieuse standard (x64)
msiexec /i "Lithotheque_Hub_Setup_x64.msi" /qn /norestart

# Installation silencieuse ARM64 (Surface ARM, Snapdragon X)
msiexec /i "Lithotheque_Hub_Setup_arm64.msi" /qn /norestart
```

### B. Emplacements Windows
* **Binaire exécutable** : `C:\Program Files\Lithotheque Hub\litho-hub.exe`
* **Données et configuration partagée** : `C:\ProgramData\Lithotheque Hub\`
* **Fichier de configuration** : `C:\ProgramData\Lithotheque Hub\config.json`
* **Fichier de licence** : `C:\ProgramData\Lithotheque Hub\license.key`
* **Base de données** : `C:\ProgramData\Lithotheque Hub\data\litho_hub.db`

### C. Gestion du Service Windows (Session 0)
Le service tourne silencieusement en arrière-plan sans ouvrir d'invite de commande CMD :
```powershell
# Vérifier le statut du service
Get-Service -Name LithoHubService

# Redémarrer le service
Restart-Service -Name LithoHubService

# Ouvrir la console de gestion des services
services.msc
```

---

## 3. Configuration du fichier `config.json`

```json
{
  "listen_addr": "127.0.0.1:8080",
  "data_dir": "/etc/litho-hub/data",
  "export_dir": "/etc/litho-hub/exports",
  "license_path": "/etc/litho-hub/license.key",
  "wireguard_port": 51820
}
```

* `listen_addr` : Adresse d'écoute locale du tableau de bord Web et des API (laisser sur `127.0.0.1:8080` pour une sécurité maximale derrière un Reverse Proxy).
* `data_dir` : Dossier racine où réside la base SQLite WAL et les conteneurs `.litho`.
* `export_dir` : Répertoire de stockage des couches SIG générées (`.gpkg`, `.csv`, `.geojson`).
* `license_path` : Emplacement de la clé Ed25519 émise par GeoStratum.
* `wireguard_port` : Port UDP d'écoute pour les communications d'enrôlement direct.
