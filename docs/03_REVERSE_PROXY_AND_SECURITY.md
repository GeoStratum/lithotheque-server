# Architecture Sécurisée & Configuration Reverse Proxy

Ce document présente la topologie de production recommandée pour exposer le serveur **Lithotheque Hub** sur Internet ou sur un réseau d'entreprise, tout en maintenant une isolation stricte des couches critiques.

---

## 1. Schéma d'Architecture Réseau & Flux

```text
[ Terminaux Mobiles Terrain (Lithothèque Android) ]
      │                                       │
      │ 1. Synchronisation Peer-to-Peer        │ 2. Consultation Dashboard / Exports
      │    (UDP 51820 Chiffré WireGuard)       │    (HTTPS 443 Chiffré TLS 1.3)
      ▼                                       ▼
┌──────────────────────────────────────────────────────────────┐
│ SERVEUR D'ACCUEIL / REVERSE PROXY                            │
│                                                              │
│  ┌────────────────────────┐      ┌────────────────────────┐  │
│  │ WireGuard Tunnel UDP   │      │ Reverse Proxy (Nginx)  │  │
│  │ Port 51820 (Direct)    │      │ Port 443 / 80 (TLS)    │  │
│  └───────────┬────────────┘      └───────────┬────────────┘  │
│              │                               │               │
│              │ (Local Kernel/Interface)      │ (Proxy Pass)  │
│              │                               │ 127.0.0.1:8080│
│              ▼                               ▼               │
│  ┌────────────────────────────────────────────────────────┐  │
│  │ LITHOTHEQUE BASE CAMP HUB (Headless Daemon / Service)  │  │
│  │                                                        │  │
│  │  • Moteur Stéréonet Float64 (Schmidt / Wulff)          │  │
│  │  • Contrôle de Licence Hors-Ligne Ed25519             │  │
│  │  • Base Consolidée SQLite (Room v12 WAL Mode)          │  │
│  │  • Générateur Master GeoPackage & GeoJSON              │  │
│  └────────────────────────────────────────────────────────┘  │
└──────────────────────────────────────────────────────────────┘
```

---

## 2. Déploiement Nginx (Recommandé)

Le fichier de configuration complet est disponible dans [`deploy/reverse-proxy/nginx/litho-hub.conf`](file:///d:/Dev/Lithoteque/litho-hub/deploy/reverse-proxy/nginx/litho-hub.conf).

### Points clés de la configuration Nginx :
1. **Terminaison SSL/TLS** : Utilisation exclusive de TLSv1.2 et TLSv1.3 avec chiffrement fort.
2. **Rate Limiting** :
   ```nginx
   limit_req_zone $binary_remote_addr zone=litho_limit:10m rate=10r/s;
   ```
   Protège contre le déni de service et la saturation CPU lors des calculs géologiques intensifs.
3. **Volume de transfert (Payload Size)** :
   ```nginx
   client_max_body_size 250M;
   ```
   Permet le téléchargement fluide des conteneurs `.litho` contenant des centaines de photographies géologiques haute résolution.
4. **Sonde de Surveillance `/api/health`** :
   Permet aux outils de supervision (Uptime Kuma, Zabbix, Datadog) d'interroger l'état sans être affectés par les limites de débit.

---

## 3. Déploiement Caddy (Alternative Automatique)

Le fichier Caddyfile est disponible dans [`deploy/reverse-proxy/caddy/Caddyfile`](file:///d:/Dev/Lithoteque/litho-hub/deploy/reverse-proxy/caddy/Caddyfile).

Avantage : Caddy obtient et renouvelle automatiquement les certificats Let's Encrypt / ZeroSSL sans intervention humaine.

```caddy
hub.mondomaine.org {
    encode gzip zstd
    request_body {
        max_size 250MB
    }
    reverse_proxy 127.0.0.1:8080 {
        health_uri /api/health
        health_interval 30s
    }
}
```

---

## 4. Règles de Pare-feu Minimales (UFW sous Linux)

```bash
# Bloquer tout le trafic entrant par défaut
sudo ufw default deny incoming
sudo ufw default allow outgoing

# Autoriser l'administration SSH (adapter le port si personnalisé)
sudo ufw allow 22/tcp comment "Administration SSH"

# Autoriser le Reverse Proxy Web
sudo ufw allow 80/tcp comment "HTTP Redirection"
sudo ufw allow 443/tcp comment "HTTPS Dashboard & API"

# Autoriser la synchronisation terrain WireGuard
sudo ufw allow 51820/udp comment "WireGuard Lithotheque Hub"

# Activer le pare-feu
sudo ufw enable
```

> **Attention** : Ne jamais exécuter `ufw allow 8080/tcp`. Le port 8080 doit rester strictement confiné à `127.0.0.1` (localhost).
