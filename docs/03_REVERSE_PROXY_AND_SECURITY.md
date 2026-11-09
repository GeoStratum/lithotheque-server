# Reverse Proxy, TLS & Production Security Guide

This document details how to expose **Lithotheque Base Camp Hub** securely using industry-standard reverse proxies (Nginx, Caddy), configure TLS certificates, and harden the server against unauthorized access.

---

## 1. Network Topology & Isolation Principles

By default, Lithotheque Hub listens exclusively on `127.0.0.1:8080`. This architecture ensures:
1. **Zero Unencrypted Exposure**: HTTP traffic is never broadcasted in cleartext over external networks.
2. **Encapsulated Mobile Sync**: Mobile synchronization occurs inside an encrypted WireGuard UDP tunnel on port `51820/UDP`.
3. **Mandatory TLS Termination**: All external web dashboard access must pass through a hardened reverse proxy terminating TLS 1.3.

---

## 2. Nginx Reverse Proxy Configuration

Create `/etc/nginx/sites-available/litho-hub.conf`:

```nginx
server {
    listen 80;
    server_name hub.geostratum.local hub.yourcompany.com;
    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl http2;
    server_name hub.geostratum.local hub.yourcompany.com;

    ssl_certificate /etc/ssl/certs/litho-hub.crt;
    ssl_certificate_key /etc/ssl/private/litho-hub.key;
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers HIGH:!aNULL:!MD5;

    # Security Headers (WCAG & OWASP compliant)
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header X-XSS-Protection "1; mode=block" always;
    add_header Referrer-Policy "strict-origin-when-cross-origin" always;

    # Maximum upload size for heavy field zip archives and photos
    client_max_body_size 500M;

    location / {
        proxy_pass http://127.0.0.1:8080;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_read_timeout 300s;
        proxy_send_timeout 300s;
    }
}
```

Enable the configuration:
```bash
sudo ln -s /etc/nginx/sites-available/litho-hub.conf /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
```

---

## 3. Caddy Reverse Proxy (Automatic HTTPS)

For automated Let's Encrypt or local CA certificates, create `/etc/caddy/Caddyfile`:

```caddy
hub.yourcompany.com {
    reverse_proxy 127.0.0.1:8080
    
    header {
        Strict-Transport-Security "max-age=31536000; includeSubDomains; preload"
        X-Content-Type-Options "nosniff"
        X-Frame-Options "SAMEORIGIN"
    }
    
    request_body {
        max_size 500MB
    }
}
```

Restart Caddy:
```bash
sudo systemctl reload caddy
```

---

## 4. Linux Firewall (UFW) Hardening

Configure Uncomplicated Firewall (UFW) to enforce strict boundary defense:

```bash
# Allow SSH management
sudo ufw allow 22/tcp

# Allow Web Access (Reverse Proxy only)
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

# Allow WireGuard Field Synchronization
sudo ufw allow 51820/udp

# Explicitly deny direct external access to internal port 8080
sudo ufw deny 8080/tcp

# Enable firewall
sudo ufw enable
sudo ufw status verbose
```
